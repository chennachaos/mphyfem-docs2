
Example 2 - Steady flow past a cylinder in 2D - Turek benchmark
================================================================

In this example, we simulate steady flow past a circular cylinder for a Reynolds number of 20. This is the `Test case 2D-1` of the paper [Benchmark Computations of Laminar Flow Around a Cylinder](https://wwwold.mathematik.tu-dortmund.de/lsiii/cms/papers/SchaeferTurek1996.pdf) by Schafer and Turek.


Mesh used for the simulation. P2/P1 element is used. Download [Turekcylinder2d-P2.msh](Turekcylinder2d-P2.msh).
![](Turekcylinder2d-P2-mesh.png)


The configuration file is shown below.
```
Files
{
  mesh : Turekcylinder2d-P2
}
    
Fluid Properties
{
  rho  : 1.0
  mu   : 0.001
}

Body Force
{
    value         :  0  0  0
    TimeFunction  :  1
}

Element Properties
{
    type : P2P1
}

Boundary Conditions
{
    inlet
    {
        type          :  specified
        dof           :  Xvelocity
        ! Re=100
        !value         :  6*y*(0.41-y)/0.1681
        ! Re=20
        value         :  1.2*y*(0.41-y)/0.1681
        timefunction  :  1
    }
    
    inlet
    {
        type          :  specified
        dof           :  Yvelocity
        value         :  0.0
        timefunction  :  1
    }
    
    bottomedge
    {
        type     :  wall
    }
    
    topedge
    {
        type     :  wall
    }
    
    cylinder
    {
        type     :  wall
    }
}


Time Functions
{
    
! lam(t) = p1 + p2*t + p3*sin(p4*t+p5) + p6*cos(p7*t+p8)
!
! id    t0       t1    p1   p2     p3    p4    p5    p6    p7    p8
    
   1    0.0      1.0  0.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0
   1    1.0   1000.0  1.0  0.0    0.0   0.0   0.0   0.0   0.0   0.0
    
!   2   0.0   1000.0  2.0  1.0    0.0   0.0   0.0   0.0   0.0   0.0
    
}

Solver
{
    schemetype    :  0
    
    timescheme         :  STEADY
    !timescheme        :   Galpha
    
    spectralRadius     :  0.0
    
    finalTime          :  2.0
    
    timeStep           :  0.1
    
    maximumSteps       :  10000
    
    maximumIterations  :  10
    
    tolerance          :  1.0e-5
      
    outputFrequency    :  1
    
}


Initial Conditions
{
    Xvelocity         :  0.0
    Yvelocity         :  0.0
}
    
Patch Output
{
    cylinder
}
```

The contour plot of velocity magnitude and pressure are shown below.

Contour plot of velocity magnitude.
![](Turekcylinder2d-Re20-velomagn.png)


Contour plot of pressure.
![](Turekcylinder2d-Re20-pressure.png)

