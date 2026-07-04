Inputs for incompressible flow problems
=========================================

Each simulation case requires three files stored in `inputs` subdirectory.

1. Mesh file
------------

* The meshes are generated in the `GMSH` software and the input meshes are stored in the `.msh` format.
* All the domains (volumes in 3D, surfaces in 2D and curves/lines in 1D) should be tagged using `Physical groups` feature in GMSH.
* No need to tag all the boundaries though. Only those boundaries on which boundary conditions are applied need to be tagged.

2. Petsc options file
----------------------

* A file named `petsc_options.dat`.
* Includes options for petsc solvers and preconditioners.


3. Config file
----------------
* A file named `config`.
* Contains the details of the simulation such as element type, element properties, material properties, loading, solver, time step, etc..
* The input is specified in the form of blocks.
* **Comments**:
    - Any line that starts with a `!` in the config file is not effective; it is treated as a comment.
    - It helps the user to write useful comments or to try multiple options without having to remember the options.


A sample `config` file is shown below.

::

    Files
    {
      mesh : cylinder-P2-coarse
    }

    Fluid Properties
    {
      rho  : 1.0
      mu   : 0.05
    }

    Body Force
    {
        value        :  0  0  0
        TimeFunction :  1
    }

    Element Properties
    {
        type :  P2P1
    }


    Boundary Conditions
    {
        inlet
        {
            type         :  specified
            dof          :  VX
            value        :  1
            timefunction :  1
        }
    
        inlet
        {
            type         :  specified
            dof          :  VY
            value        :  0.0
            timefunction :  1
        }
    
        bottomedge
        {
            type         :  specified
            dof          :  VY
            value        :  0.0
            timefunction :  1
        }
    
        topedge
        {
            type         :  specified
            dof          :  VY
            value        :  0.0
            timefunction :  1
        }
    
        cylinder
        {
            type        :  wall
        }
    }


    Time Functions
    {

    ! lam(t) = p1 + p2*t + p3*sin(p4*t+p5) + p6*cos(p7*t+p8)
    !
    ! id   t0      t1     p1   p2     p3    p4    p5    p6    p7    p8
       1   0.0   1000.0  0.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0

    !   1   0.0      1.0  0.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0
    !   1   1.0   1000.0  1.0  0.0    0.0   0.0   0.0   0.0   0.0   0.0

    }


    Solver
    {
      schemetype    :  0

      !timescheme        :  BDF2
      !timescheme        :  Galpha
      !timescheme        :  Midpoint
      timescheme        :  STEADY

      spectralRadius     :  0.0
    
      finalTime          :  300.0
    
      timeStep           :  0.1
    
      maximumSteps       :  10
    
      maximumIterations  :  10
    
      tolerance          :  1.0e-7
    
      CFL                :  1.0
      
      outputFrequency    :  1
    
      debug              :  0
    
    }

    Initial Conditions
    {
        Xvelocity       :  1.0
        Yvelocity       :  0.0
    }

    Patch Output
    {
        cylinder
    }




Different blocks are discribed below. Each block starts with `{` and ends with `}` on separate lines.


Files
******
Specifies the input mesh file and previous solution file for in case of `restart`. Different fields are

* **msh**: Prefix of the mesh file. *Compulsory*.
* **restart**: the name of the restart file with the solution. *Optional*.


Fluid Properties
****************
Specifies the properties of the fluid, such as density and viscosity.

* **rho**: <density of the fluid>
* **mu**: <dynamic viscosity>

::

    block
    {
      rho    :  1.0
      mu     :  0.01
    }


Body Force
**************
Specifies the body force.

* **value**: Value of the body force as a vector given as three values.
* **TimeFunction**: Time function id in case the body force is to be applied as a function of time. *Optional field*. Applied as the constant field if the `TimeFunction` is not specified.


Boundary Conditions
********************
Specifies the Dirichlet boundary conditions on the boundary patches.

The syntax is ::

    <patch-tag>
    {
        type          : <type>
        dof           : <DOF>
        value         : <value>
        TimeFunction  : <value>
    }


Available fields are:

* **type**: Boundary condition type.
    * `wall`: to specify wall BC on the patch. All velocity DOFs are set to zero.
    * `specified`: to prescribe a value for a particular DOF.
* **dof**: Degree of freedom.
    * VX: X-Velocity
    * VY: Y-Velocity
    * VZ: Z-Velocity
* **value**: The value to be applied.
* **timefunction**: To apply the boundary condition as a function of time. If not specified, boundary condition is applied as a constant value over time.


The following block specifies a constant value of 2.0 for X-Velocity on the patch tagged as `inlet`, and it will be applied as a time-varying value as defined by the `time function` with `id 1`. ::

    inlet
    {
        type          : specified
        dof           : VX
        value         : 2.0
        timefunction  : 1
    }

The following block specifies a parabolic profile for X-Velocity on the patch tagged as `inlet`, and it will be applied as a time-varying value as defined by the `time function` with `id 1`. ::

    inlet
    {
        type          : specified
        dof           : VX
        value         : 6*y*(0.41-y)/0.1681
        timefunction  : 1
    }

The following block specifies wall BC on the patch tagged as `cylinder`. ::

    cylinder
    {
        type          : wall
    }

The following block specifies a value of 0 (zero) for Y-Velocity on the patch tagged as `bottomedge`. ::

    bottomedge
    {
        type          : specified
        dof           : VY
        value         : 0
    }


The following block specifies a value of 2.0 for Z-velocity on the patch tagged as `inlet`, and it will be applied as a time-varying value as defined by the `time function` with `id 2`. ::

    inlet
    {
        type          : specified
        dof           : VZ
        value         : 2.0
        timefunction  : 2
    }


Time functions
**************
This block contains the time functions for time-dependent boundary/loading or conditions or other time-dependent parameters. The time functions are specified as shown below by providing the coefficients p1, p2, p3, p4, p5, p6, p7 and p8. *p4* and *p7* are angular frequencies in radians per time units. *p4=p7=2 \pi f*, with f as the frequency. Phase angles *p5* and *p8* should be in radians.

::

    ! f(t) = p1 + p2*t + p3*sin(p4*t+p5) + p6*cos(p7*t+p8)
    !
    ! id   t0      t1     p1   p2     p3    p4    p5    p6    p7    p8
       1   0.0   1000.0  0.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0

       2   0.0      1.0  0.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0
       2   1.0   1000.0  1.0  0.0    0.0   0.0   0.0   0.0   0.0   0.0

       3   0.0   1000.0  0.0  0.0    2.0   6.2832   3.1416   0.0   0.0   0.0


The above block specifies three different time functions as given by the ids 1, 2 and 3.

* Time function 1: Constant value until 1000 time units.
* Time function 2: Linear variation with a slope of 0.5 until 2 time units and then constant value of 1.0 until 1000 time units.
* Time function 3: Sinusoidal variation with an amplitude of 2.0 and frequency of 1.0 Hz and phase angle of 3.1416 radians (180 degrees).


Solver
********
This block specifies what type of solver, nonlinear scheme, time step size, output frequency, max iterations, end time etc. Available fields are

* **schemetype**: The type of the scheme to be used.
    * 0: Velocity-pressure mixed formulation with linearised convection operator.
    * 2: Projection scheme.
* **timescheme**: time integration scheme to be used.
    * STEADY: Steady-sate problem. No time integration.
    * BDF1: Backward-Euler scheme. This is a first-order scheme. Unconditionally stable.
    * BDF2: Backward difference formula. Second-order accurate scheme. Unconditionally stable.
    * BDF3: Backward difference formula. Third-order accurate scheme. Unconditionally stable.
    * Galpha: Generalised-alpha scheme. Second-order accurate. Unconditionally stable.
* **spectralRadius**: Spectral radius value. Valid only for the Galpha scheme. Range is between 0 and 1, inclusve. A value of zero annihialites all high-frequency modes. A value of one add no numerical damping.
* **finalTime**: The final time until which the simulation should be run.
* **timeStep**: The time step size.
* **maximumSteps**: Maximum number of time/load steps.
* **maximumIterations**: Maximum number of iterations per time/load step.
* **tolerance**: Convergence tolerance for iterations.
* **debug**: A flag used to print extra output for debugging purposes.
    * 0 for deactivating debugging info.
    * 1 for activating debugging info. This will be print some additional information to the output screen to help in debugging the code. *Only useful during development*.

**NOTE**: The simulation will run until whichever of **finalTime** and **maximumSteps** is satisfied first.
