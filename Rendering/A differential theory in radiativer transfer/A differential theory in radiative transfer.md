# A Differential theory in radiative transfer

## 1.Preliminaries

### 1.1 Radiative Transfer

Consider a medium confined in volume $\Omega\subseteq \R^3$with boundary$\partial \Omega$.

**The interior radiance field**: $L(\mathbf{x}, \mathbf{\omega})$ is defined on $\mathbf{x}\in \Omega \backslash \partial \Omega$  and  $\mathbf{\omega}\in \mathcal{S}^2$

The Steady-state RTE(Radiative transfer equation)can be expressed as:
$$
L = \mathcal{K}_T\mathcal{K}_CL+Q\tag{1,steady-state RTE}
$$
where $K_T$ is  **transport operator** maps $g:\Omega \backslash \partial \Omega\times \mathcal{S}^2\to\R_+$ :
$$
(\mathcal{K}_Tg)(\mathbf{x},\mathbf{\omega}) =\int_0^DT(\mathbf{x^\prime},\mathbf{x})g(\mathbf{x^\prime},\mathbf{\omega}) d\tau\tag{2}
$$
in Eq(2)， $\mathbf{x^\prime}:=\mathbf{x}-\tau\bf{\omega}$，$D$ is the distance from $\mathbf{x}$ to the boundary of the volume:
$$
D = \inf\{\tau\in \R^+:\mathbf{x}-\tau\omega\in\partial \Omega\}\tag{3}
$$
and Eq(2)can be expressed as:
$$
(\mathcal{K}_Tg)(\mathbf{x},\mathbf{\omega}) =\int_0^DT(\mathbf{\mathbf{x}-\tau\omega},\mathbf{x})g(\mathbf{\mathbf{x}-\tau\omega},\mathbf{\omega}) d\tau
$$
 and $T (x ′ , x)$ is the transmittance between $x ′$ and $x$, that is, the fraction of light that transmits straight between $x ′$ and $x$ without being absorbed or scattered away:  
$$
T(\mathbf{x^\prime},\mathbf{x})=T(\mathbf{x}-\tau\omega,\mathbf{x}):=\exp(-\int_0^{\tau}\sigma_t(\mathbf{x}-\tau^\prime\omega)d\tau^\prime)\tag{4}
$$
 with $σ_t$ denoting the medium’s **extinction coefficient**. 

$\mathcal{K}_C$：collision operator maps the internal radiance as:
$$
(\mathcal{K}_CL)(\mathbf{x},\mathbf{\omega}) =\sigma_s(\mathbf{x})\int_{\mathcal{S}^2}f_p(\mathbf{x}, -\omega^\prime, \omega)L(\mathbf{x},\omega^\prime)d\omega^\prime\tag{5}
$$
It might need to be explained :$L(\mathbf{x},\omega^\prime)$ and $f_p(\mathbf{x}, -\omega^\prime, \omega)$. Here $L$ is $L_i$， so the $\omega$ vector is point at the income direction; which is opposite from the variant`s value in BSDF function.

where $\sigma_s$is scattering coefficient and $f_p$ is single-scattering phase function. We use notation $L^{ins}$ denotes **in-scattered radiance**:
$$
L^{ins}:=\int_{\mathcal{S}^2}f_p(\mathbf{x}, -\omega^\prime, \omega)L(\mathbf{x},\omega^\prime)d\omega^\prime
$$
The pic below can be seen as a reference to the mentioned terms:

![1694782216024](A%20differential%20theory%20in%20radiative%20transfer.assets/1694782216024.png)

 The source term $Q$ of the RTE (1) is sometimes assumed given for problems with simple geometries (hence the name). Generally, with the presence of nontrivial interfaces, $Q$ equals to:
$$
Q(\mathbf{x}, \omega) = \int_o^DT(\mathbf{x^\prime},\mathbf{x})\sigma_a(\mathbf{x^\prime})L^e(\mathbf{x^\prime},\mathbf{\omega}) d\tau + T(\mathbf{x_0},\mathbf{x})L_s(\mathbf{x_0},\omega)\tag{6}
$$
  where $x_0 := x − Dω$ is a point on the medium’s boundary, and $σ_a := σ_t−σ_s$ is the **absorption coefficient**. On the right-hand side (RHS) of this equation, $L^e$ represents the **medium’s** **radiant emission**, and $L^s$ indicates the **interfacial radiance** governed by the rendering equation (RE) for all $ x ∈ ∂Ω$ and $ω ∈ S  $
$$
L_s(\mathbf{x}, \omega) = \int_{\mathcal{S}^2}f_s(\mathbf{x}, -\omega^\prime, \omega)L(\mathbf{x},\omega^\prime)d\omega^{\perp}+L_s^e(\mathbf{x},\omega)\tag{7,RE}
$$

$$
L_s^r := \int_{\mathcal{S}^2}f_s(\mathbf{x}, -\omega^\prime, \omega)L(\mathbf{x},\omega^\prime)d\omega^{\perp}
$$

 $L_s^e$ denotes the interfacially emitted radiance, and $L_s^r$ indicates the refected/refracted radiance.  

We can then combine RE and RTE:
$$
Q = \mathcal{K}_SL+L^{(0)}\tag{8}
$$
where $\mathcal{K}_s$ is the **interfacial operator**,defined as:
$$
(\mathcal{K}_sL)(\mathbf{x}, \omega):= T(\mathbf{x_0},\mathbf{x})L_s^r(\mathbf{x_0},\omega)\\
=T(\mathbf{x_0},\mathbf{x})\int_{\mathcal{S}^2}f_s(\mathbf{x}, -\omega^\prime, \omega)L(\mathbf{x},\omega^\prime)d\omega^{\perp}\tag{9}
$$
 **which accounts for contributed radiance due to refection and transmission at the medium’s boundary.** The second term in Eq. (8) is 
$$
L^{(0)}:=\mathcal{K}_T\sigma_aL^e+T(\mathbf{x_0},\mathbf{x})L_s^e(\mathbf{x},\omega)\tag{10}
$$
**indicating the contribution of radiant emission in the medium and from its boundary.**  

So we finally yield the operator form of RTE and RE combined:
$$
L=(\mathcal{K}_T\mathcal{K}_C+\mathcal{K}_S)L+L^{(0)}\tag{11}
$$

### 1.2 Scene derivatives

We assume that the scene specifications (such as object geometries and material properties) are **parameterized** by a set of m parameters $\textbf{π} = \{π_1, π_2, . . . , π_m \}$, and each parameter varies continuously. The medium’s domain and its boundary may also vary with these parameters. We therefore denote them as $Ω(\textbf{π})$ and $∂Ω(\textbf{π})$, respectively 

Provided a specific scene parameterization, we aim to develop a theory for computing the total derivative (or gradient) of the interior radiance $L $with respect to $π$. We denote the total derivative as $d_π L ∈ \R^m $and refer it as the scene derivative of $L$. 

Let Interior radiance $L(\mathbf{x}, \omega;\textbf{π})$defined on $:(\Omega(\textbf{π})\backslash\partial\Omega(\textbf{π}))\times \mathcal{S}^2$ Its total derivative is $d_{\pi}L(\mathbf{x(\textbf{π})},\omega(\textbf{π}), \textbf{π})$, which is a $m\times 1$vector whose i-th element is:
$$
\partial_{\pi_i}L:=\frac{\partial L}{\partial\pi_i} =\lim_{\epsilon\to 0}\frac{L(\mathbf{x(\textbf{π}_i^\prime)},\omega(\textbf{π}_i^\prime), \textbf{π}_i^\prime)-L(\mathbf{x(\textbf{π})},\omega(\textbf{π}), \textbf{π})}{\epsilon}
$$
here $\textbf{π}_i^\prime = \{π_1, π_2, . .\pi_i+\epsilon. , π_m\}$

**We note that it is crucial to allow both x and ω to depend on the scene parameters π. As we will show later, this dependence emerges in a number of interesting applications such as (i) interfacial radiances Ls on evolving surfaces and (ii) radiometric measurements from moving sensors.** 

Without loss of generality: We can focus on one element  $\pi\in\textbf{π}$. Hence we can denote $\partial_{\pi}L$ as $\dot{L}$ 

### 1.3 Differential Integrals

**Theorem 1: Reynolds Transport Theorem**(Comes from fluid mechanics)

Let $f$ be a scalar-valued function defined on some n-dimensional manifold $Ω(π)$ parameterized with some $π ∈ R$. Additionally, let$ Γ(π) ⊂ Ω(π)$ be an $(n − 1)$-dimensional manifold given by the union of the external boundary $∂Ω(π) $and the internal one containing the discontinuous locations of $f$ . Then, it holds that 
$$
\partial_{\pi}(\int_{\Omega{(\pi)}}f d\Omega(\pi))=\int_{\Omega(\pi)}\dot{f}d\Omega({\pi})+\int_{\Gamma(\pi)}\langle\mathbf{n}\cdot \mathbf{\dot x}\rangle\Delta f\text{ d}\Gamma(\pi)\tag{13}
$$
 where $\dot{f }:= ∂_π f$ , $\dot{x}:= ∂_π x$, $dΩ$ and $dΓ$ respectively denote the standard measures associated with$ Ω $and $Γ$; and $⟨·, ·⟩$ indicates the dot (inner) product between two vectors. Further,$ n $is the normal direction at each $x ∈ Γ(π)$, and $∆f $is given by a
$$
∆f (\mathbf{x}) := \lim _{ϵ→0^-}  f (\mathbf{x} + ϵ \mathbf{n}) − \lim _{ϵ→0^+}  f (\mathbf{x} +\epsilon\mathbf{ n}) \tag{14}
$$
for all x ∈ Γ(π) 

Special Case: 1-dimensional:
$$
\partial_{\pi}(\int_{a(\pi)}^{b(\pi)}fdx) = \int_{a(\pi)}^{b(\pi)}\dot{f}dx+f(b(\pi), \pi)b^\prime(\pi)-f(a(\pi),\pi)a^\prime(\pi)\tag{15}
$$
which requires that: 

1. $f$ and $\dot{f}$ is continuous
2. $a(\pi)$ and $b(\pi)$ is differentiable

## 2. Differentiable Radiative Transfer

We now present our differential theory of radiative transfer that shows how the interior radiance $ L $ can be differentiated with respect to some scene parameter $ π ∈ \R$. To this end, we derive the partial derivative $ \dot{L} := ∂_π L $ by differentiating each of the operators on the right-hand side (RHS) of Eq. (11).   

 **Assumptions**. 

1. As most participating media and translucent materials are non-emissive, **we neglect the volumetric emission term** $L^e$ in Eqs. (6) and (10) when deriving $\dot{L} $ in this section. Additionally, we assume that: 

2.  the RTE and RE parameters $σ_t , σ_s, f_p, L^e , f_s, \text{and }L^e_s $ are **continuous spatially and directionally**; and 

3.  **there are no zero-measure light sources** (e.g., point and directional) or ideal specular surfaces (e.g., perfect mirrors). We generalize our derivations by relaxing some of these assumptions in the supplement. 

### 2.1 Differentiation of the Transport & Collision Operators

#### 2.1.1 Deriving $\partial_{\pi}\mathcal{K}_T\mathcal{K}_C$

$$
\mathcal{K}_T\mathcal{K}_CL = \int_0^DT(\mathbf{x^\prime}, \mathbf{x})\sigma_s(\mathbf{x^\prime})L^{ins}(\mathbf{x^\prime},\omega)d\tau\tag{16}
$$

According to the assumption2,  the integrand on rhs is continuous in $\tau$, for $0\lt \tau \lt D$

Thus applying Theorem 1 (Eq.15) to Eq.16 gives:
$$
\partial_{\pi}(\mathcal{K}_T\mathcal{K}_CL ) = \int_0^D\partial_{\pi}[T(\mathbf{x^\prime}, \mathbf{x})\sigma_s(\mathbf{x^\prime})L^{ins}(\mathbf{x^\prime},\omega)]d\tau\tag{17}+T(\mathbf{x-}D\omega,\mathbf{x})\sigma_s(\mathbf{x}-D\omega)L^{ins}(\mathbf{x}-D\omega,\omega)\cdot\dot{D}
$$

#### 2.1.2   Differentiating Transmittance 

Review:
$$
T(\mathbf{x^\prime},\mathbf{x})=T(\mathbf{x}-\tau\omega,\mathbf{x}):=\exp(-\int_0^{\tau}\sigma_t(\mathbf{x}-\tau^\prime\omega)d\tau^\prime)\tag{4}
$$
 Applying Theorem 1 to this equation leads to:
$$
\dot{T}(\mathbf{x}-\tau\omega,\mathbf{x}) = -T(\mathbf{x}-\tau\omega,\mathbf{x})[\int_0^\tau\dot{\sigma_t}(\mathbf{x}-\tau^\prime\omega)d\tau^\prime+\dot{\tau}\sigma_t(\mathbf{x-\tau\omega})]\tag{18}
$$
Here, we introduce a new notation to simplify Eq.18
$$
\Sigma_{t}(\mathbf{x},\omega,\tau) = \int_0^{\tau}\dot{\sigma_t}(\mathbf{x}-\tau^\prime\omega)d\tau^\prime \tag{19}
$$
Since $\dot{\tau}=0$ we have(**Why?**）:
$$
(\partial_{\pi}\tau)\cdot\sigma_t(\mathbf{x}-\tau\omega) = 0
$$

$$
\dot{T}(\mathbf{x}-\tau\omega,\mathbf{x}) = -T(\mathbf{x}-\tau\omega,\mathbf{x})\Sigma_{t}(\mathbf{x},\omega,\tau)\tag{20}
$$

**Why: **:  $\tau$is a golem variable, which means it is independent from $\pi$

Dont understand? Look at this Example:
$$
F(x) = \int_0^{x^2}xf(t)dt
$$
Then:
$$
F^{\prime}(x) = \int_0^{x^2}f(t)+(x\frac{df}{dx})dt + xf(x^2)2x = \int_0^{x^2}f(t)+xf(x^2)2x
$$

#### 2.1.3

According to Eq.17 and 20, we have:
$$
\partial_{\pi}(\mathcal{K}_T\mathcal{K}_CL ) = \int_0^D T(\mathbf{x^\prime}, \mathbf{x})\sigma_s(\mathbf{x^\prime})[\partial_{\pi}L^{ins}(\mathbf{x^\prime},\omega)]d\tau
\\
+\int_0^DT(\mathbf{x^\prime}, \mathbf{x})[\partial_{\pi}\sigma_s(\mathbf{x^\prime})-\Sigma_t(\mathbf{x^\prime},\omega,\tau)\sigma_t(\mathbf{x^\prime})]          L^{ins}          d\tau
\\
+T(\mathbf{x-}D\omega,\mathbf{x})\sigma_s(\mathbf{x}-D\omega)L^{ins}(\mathbf{x}-D\omega,\omega)\cdot \partial_{\pi}D\tag{21}
$$

In Eq.17 $\partial_{\pi}\sigma$ is essentially material derivative
$$
\dot{\sigma}(\mathbf{x}) = \frac{\partial\sigma}{\partial\pi}+\langle\dot{\mathbf{x}},\nabla\sigma(\mathbf{x})\rangle\tag{22}
$$
for $\sigma\in\{\sigma_s,\sigma_t\}$

Then, we still need to know how to calculate $\dot{D}, L^{ins} and \dot{L}^{ins}$ . In what follows, we derive formulas for these terms. 

#### 2.1.4 Derivation of D

 In the transport operator of Eq. (2), the upper bound of the line integral D is determined by the distance a light ray originating at $\mathbf{x}$ with direction $−ω$ travels before intersecting the medium boundary $∂Ω$. Suppose that the boundary $∂Ω(π)$ has an implicit representation $F (y; π) = 0$ parameterized by $π$. Then, substituting $y$ with the ray equation yields $F (x − Dω; π) = 0$ and $D = F^{−1} (0; x,ω, π)$, with $F^{ −1}$ being the inverse function of $F$ with respect to $D$. Then, $\dot{D} = ∂_π F^{−1} (0; \mathbf{x},ω, π)$. 

 For piecewise-linear shapes, such as polygonal meshes, it holds that:
$$
F(\mathbf{y};\pi) = \langle\mathbf{n_{face}},\mathbf{y}-\mathbf{p_{face}}\rangle
$$
so,
$$
F(\mathbf{x}-D\omega;\pi) = \langle\mathbf{n_{face}},\mathbf{x}-D\omega-\mathbf{p_{face}}\rangle = \langle\mathbf{n_{face}},\mathbf{x}-\mathbf{p_{face}}\rangle-D\langle\mathbf{n_{face}},\omega\rangle\tag{23}
$$
therefore:
$$
\dot{D} = \partial_{\pi}\frac{\langle\mathbf{n_{face}},\mathbf{x}-\mathbf{p_{face}}\rangle}{\langle\mathbf{n_{face}},\omega\rangle}\tag{24}
$$
 In practice, $\dot{\text{D}}$ can also be obtained by diferentiating the ray tracing process using automated diferentiation. **(Why?)**

#### 2.1.5  In-scattered radiance at the boundary

 The last term required for evaluating $∂_π \mathcal{K}_T \mathcal{K}_C L$ in Eq. (21) involves the in-scattered radiance $L^{ ins}$ at boundary point $\mathbf{x}_0$. Caution is needed here since light transport behaves differently at the two sides of the boundary. $L^{ins}(\mathbf{x}_0,ω)$ is given by the limit of $L^{ins}(\mathbf{x}^{\prime},ω)$ as $\mathbf{x^′}$ approaches the boundary location $\mathbf{x}_0$ from the interior of the medium along the direction of $−ω$. Namely, $L^{ins}(\mathbf{x}_0,ω)$ = $\lim_{τ→D^-} L^{ins}(\mathbf{x} ′ ,ω) $with $\mathbf{x}^′ := \mathbf{x} − τω$. This limit can be further expressed as 
$$
L^{ins} = \int_{\H^+}f_p(\mathbf{x}_0,-\omega^\prime,\omega)L(\mathbf{x_0},\omega^\prime)d\omega^\prime+\\
\int_{\H^-}f_p(\mathbf{x_0},-\omega^\prime,\omega)L_s(\mathbf{x_0,\omega^\prime})d\omega^\prime\tag{25}
$$
<img src="A%20differential%20theory%20in%20radiative%20transfer.assets/1695111795313.png" alt="1695111795313" style="zoom:67%;" />

Here,
$$
\H^+=\{\omega\in \mathcal{S}^2, \mathbf{n(x_0)\cdot\omega<0}\}\\
\H^-=\{\omega\in \mathcal{S}^2, \mathbf{n(x_0)\cdot\omega>0}\}\tag{26}
$$
 where $\mathbf{n(x0)}$ is the boundary normal pointing toward the exterior of the medium (i.e., $⟨\mathbf{n(x0)},ω⟩ < 0$). 

### 2.2  Differentiation of the In-Scattered Radiance

Evaluating the RHS of Eq. (21) also requires the derivative of the in-scattered radiance $L^{ins} $of Eq. (5). Recall that $L^{ins}$ is expressed as an integral of $f_p(\mathbf{x}, −ω′ ,ω) L(\mathbf{x},ω′ ) $over directions $ω′$ . **Note that $L(\mathbf{x},ω′ )$ may have discontinuities in $ω′$ (for fixed $\mathbf{x}$) due to visibility changes.** Thus, to differentiate $L^{ins}$, **we must consider how the discontinuities change with respect to the scene parameter π** 

