
Formulation for FSI problems using Fictitious Domain Method (FDM)
==================================================================

The formulation used for solving the FSI problems involving thin structures is the fictitious domain method (FDM). This is similar to the immersed boundary method (IBM) and it differs with IBM in the way the interface velocity BC is applied.

In the FDM, the interface velocity continuity condition is applied using the Lagrange multipliers.


The block matrix system of equations after spatial and temporal discretisation can be written as

$$
\begin{align}
\begin{bmatrix}
\mathbf{K}_{uu}  &  \mathbf{K}_{up}  &  \mathbf{K}_{u\lambda}  \\
\mathbf{K}_{pu}  &  \boldsymbol{0}   &  \boldsymbol{0}  \\
\mathbf{K}_{\lambda u}  &  \boldsymbol{0}   &  \boldsymbol{0}  \\
\end{bmatrix}
\;
\begin{Bmatrix}
\mathbf{u} \\
\mathbf{p} \\
\boldsymbol{\lambda}
\end{Bmatrix}
=
\begin{Bmatrix}
\mathbf{F}_{u} \\
\mathbf{F}_{p} \\
\mathbf{F}_{\lambda}
\end{Bmatrix}
\end{align}
$$

