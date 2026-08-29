---
layout: post
title: Grassmannian and AI Interpretability
permalink: /blog/2026/grassman-and-ai-interpretability-en/
mini-title: Interpretability Geometry
---


## 1. Block-Sparse Featurizer

[Block-Sparse Featurizer (BSF)](https://www.goodfire.com/research/bsf-vision), introduced in recent work on block-sparse visual concept manifolds <citation key="structuring-sparsity"></citation>, is particularly interesting because it takes the **geometry of neural representations** seriously. Rather than assuming that an interpretable concept should correspond to a single direction in activation space, BSF allows a concept to occupy a **low-dimensional space with its own internal geometry**.

This seems especially natural in vision. Visual properties such as pose, viewpoint, orientation, shape, lighting, or texture vary continuously while still belonging to the same semantic concept. An object does not stop being the same object as its pose or viewpoint changes. It is therefore plausible that such concepts are represented not along a single scalar axis, but as structured low-dimensional regions inside a much larger activation space.

There is a useful historical connection here. Earlier work on curve detectors <citation key="curve-detectors"></citation> is a classic example of early neuron-level interpretability in vision: individual neurons in InceptionV1 were found to respond selectively to curves with different orientations. BSF revisits this example and shows that these apparently separate curve detectors can instead be understood as reading from a **shared continuous curve manifold**. What looked like a collection of isolated features may therefore be different views of a single geometric object.

A similar departure from one-dimensional features has appeared in language models. Some LLM features appear to be inherently multidimensional <citation key="multidimensional-features"></citation>. In particular, researchers have identified circular representations for concepts such as days of the week and months of the year, with intervention evidence that these structures participate in computations such as modular arithmetic.

More recent work from Anthropic <citation key="counting-manifolds"></citation> provides another interesting LLM example in which internal computation is organized through structured low-dimensional geometry.

Together, these works suggest a broader shift in interpretability. Instead of asking only

> Which neuron or direction represents a concept?

we can also ask

> What geometric object represents the concept, and how does the model compute with that geometry?

This is also what makes Goodfire's recent direction particularly compelling. Geometry is not treated merely as something to visualize after extracting features; it becomes a **design constraint for the featurizer itself**.

The theoretical motivation comes from work on the duality between sparse autoencoders and concept geometry <citation key="projecting-assumptions"></citation>, which emphasizes a deep connection between the architecture of a featurizer and the geometry it assumes neural concepts to have. A featurizer is therefore not a neutral tool: choosing its architecture also means choosing which kinds of representational geometry it is well suited to discover.

BSF combines this perspective with the Additive Mixture of Manifolds hypothesis <citation key="concept-manifolds"></citation>, where an activation is viewed as the sum of a small number of active manifold-valued factors. The formal versions of these assumptions are given at the end of this post.

The architectural consequence is simple: if a concept may be multidimensional, then the atomic unit of the featurizer should also be multidimensional.

For a block $g$, BSF uses

$$
D_g\in\mathbb{R}^{k\times d},
$$

whose $k$ rows jointly define a low-dimensional representational subspace. Given an activation $x\in\mathbb{R}^d$, the block coordinates are

$$
z_g=D_gx\in\mathbb{R}^k.
$$

Instead of sparsifying the coordinates $z_{g,i}$ independently, BSF treats the entire block as one unit. Its activity is measured through the block norm

$$
\|z_g\|_2
=
\sqrt{\sum_{i=1}^{k}z_{g,i}^2}.
$$

Only a small number of blocks are retained, while the coordinates inside an active block remain free to vary. This creates a useful separation between **which concept is active** and **where the representation lies within that concept**.

In this sense, BSF is loosely reminiscent of a **Mixture-of-Experts (MoE)** architecture. MoE sparsely selects computational experts, whereas BSF sparsely selects representational factors or subspaces. The analogy is not exact—a BSF block is not an independent neural network—but both approaches explain a complex state through a small number of specialized components.


<figure>
    <img src="https://d2acbkrrljl37x.cloudfront.net/MatrixFigures/Research/bfs-and-grassmannian.webp" />
    <figcaption>
    <figtitle>Block-Sparse Featurizer and its Grassmannian interpretation.</figtitle>
    <figdetail>
Instead of representing a concept with a single feature direction, BSF assigns it a low-dimensional block. For an input representation $x$, each block produces coordinates $z_g=D_gx$, while block sparsity retains only a small number of strongly activated factors. The magnitude $\|z_g\|_2$ describes block-level activation, while the coordinates inside the block preserve multidimensional variation within the corresponding concept.
    </figdetail>
    </figcaption>
</figure>


## 2. Encoder–Decoder Matching and Subspace Projection

One of the most interesting properties of the **Grassmannian BSF** is the matching between the encoder and decoder. Using column-vector notation and omitting the learned global scale for simplicity, the encoder maps an input representation into block coordinates as $z_g=D_gx$, while the decoder maps those coordinates back using the transpose of the same basis, $\hat{x}_g=D_g^\top z_g$.

In other words, the same matrix determines both how the representation is **decomposed** and how it is **recombined**.


---

### Example

Consider a block whose basis is not aligned with the original coordinate axes:

$$
D_g=
\begin{bmatrix}
\frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} & 0\\
0 & 0 & 1
\end{bmatrix},
\qquad
x=
\begin{bmatrix}
4\\
1\\
3
\end{bmatrix}.
$$

The two rows of $D_g$ form an orthonormal basis for a two-dimensional subspace. The encoder first expresses $x$ in this basis:

$$
z_g
=
D_gx
=
\begin{bmatrix}
\frac{5}{\sqrt{2}}\\
3
\end{bmatrix}.
$$

The matched decoder then maps these coordinates back into the original representation space:

$$
D_g^\top z_g
=
\begin{bmatrix}
\frac{5}{2}\\
\frac{5}{2}\\
3
\end{bmatrix}.
$$

The result is not the original vector $x$, but its projection onto the subspace represented by the block. The encoder therefore describes $x$ **within a particular subspace**, while the decoder reconstructs exactly the component of $x$ explained by that same subspace.

---

More generally, if the rows of $D_g$ are orthonormal, so that $D_gD_g^\top=I_k$, encoding followed by decoding gives

$$
\hat{x}_g
=
D_g^\top D_gx
=
P_gx,
\qquad
P_g:=D_g^\top D_g.
$$

Here, $P_g$ is the orthogonal projection onto the $k$-dimensional subspace spanned by the rows of $D_g$.

Importantly, when $k<d$, the condition $D_gD_g^\top=I_k$ does **not** imply $D_g^\top D_g=I_d$. Instead, $D_g^\top D_g$ is a rank-$k$ projection matrix: it preserves the component of $x$ inside the block subspace and removes the component orthogonal to it.

The operation can therefore be understood as

$$
x
\;\longrightarrow\;
D_gx
\;\longrightarrow\;
D_g^\top D_gx,
$$

moving from the original representation to **coordinates inside a subspace**, and then back to the **part of the representation explained by that subspace**.

This gives encoder–decoder sharing a clean geometric interpretation. The encoder determines how $x$ is decomposed within the subspace, while the decoder determines how that same component is recombined in the original activation space. Because both operations use the same basis, they describe the same geometric object.

For multiple active blocks, reconstruction takes the form

$$
\hat{x}
=
\sum_{g\in\mathcal{A}}D_g^\top z_g,
$$

where $\mathcal{A}$ denotes the selected blocks. BSF therefore decomposes an activation into a sparse set of subspace-level contributions and then recombines them.


## 3. Grassmannian and Basis-Rotation Invariance

The next important distinction is between a particular **basis** $D_g$ and the **subspace** represented by that basis. The rows of $D_g$ form an orthonormal basis for a $k$-dimensional subspace of $\mathbb{R}^d$, but this basis is not unique.

For any orthogonal matrix $Q\in O(k)$, we can rotate the basis inside the block by defining

$$
D'_g=QD_g.
$$

The individual basis directions change, but the subspace does not:

$$
\operatorname{span}(D'_g)
=
\operatorname{span}(QD_g)
=
\operatorname{span}(D_g).
$$

The coordinates inside the block do depend on this choice of basis. Since $z_g=D_gx$, the rotated basis gives

$$
z'_g
=
D'_gx
=
Qz_g.
$$

However, the block magnitude remains unchanged because an orthogonal transformation preserves the $\ell_2$ norm:

$$
\|z'_g\|_2
=
\|Qz_g\|_2
=
\|z_g\|_2.
$$

Thus, rotating the internal basis does not affect whether the block is selected.

More importantly, the reconstructed component is also invariant:

$$
D_g'^\top D'_g
=
D_g^\top Q^\top QD_g
=
D_g^\top D_g
=
P_g.
$$

The internal coordinates can therefore rotate freely without changing either **which block is active** or **which component of the original representation the block explains**.

This leads naturally to the Grassmannian. The Grassmannian

$$
\mathrm{Gr}(k,d)
=
\left\{
V\subseteq\mathbb{R}^d
\mid
\dim(V)=k
\right\}
$$

is the space of all $k$-dimensional linear subspaces of $\mathbb{R}^d$.

A matrix $D_g$ gives one orthonormal frame for such a subspace, but every rotated frame $QD_g$ represents exactly the same point on the Grassmannian. Equivalently, we can think of a feature as the equivalence class

$$
[D_g]
=
\{QD_g\mid Q\in O(k)\}.
$$

The meaningful object is therefore not a particular choice of basis, but

$$
V_g=\operatorname{span}(D_g).
$$

The basis is simply a coordinate system used to describe the feature; the **subspace itself is the geometric object**.


## 4. What This Means for Interpretability

The Grassmannian perspective changes the basic unit of interpretation. Conventional sparse representations encourage us to assign semantic meaning to individual directions. BSF instead suggests that some concepts may be more naturally understood as **structured low-dimensional objects**.

This is particularly useful for continuous concepts. The block magnitude $\|z_g\|_2$ can indicate **whether a factor is active**, while the position or direction of $z_g$ inside the block can describe **how that factor is instantiated**.

The curve-detector example makes this distinction concrete. What earlier neuron-level analysis <citation key="curve-detectors"></citation> described as several units tuned to different curve orientations can, under the BSF interpretation, be understood as different readouts from a shared continuous geometry.

Importantly, the block subspace should not itself be confused with the concept manifold. BSF assumes that a concept manifold is contained within a relatively low-dimensional linear span. Inside that span, the actual representation may still trace a circle, sphere, curved surface, or some other nonlinear structure. The block provides the **coordinate space in which this internal geometry can live**.

This gives a broader interpretation of block sparsity:

- sparsity across blocks captures **which conceptual factors are present**;
- coordinates within a block capture **variation inside the factor**;
- encoder–decoder matching makes the representation independent of an arbitrary choice of basis.

The key shift is therefore

$$
\boxed{
\text{isolated feature direction}
\;\longrightarrow\;
\text{structured concept geometry}
}
$$

Rather than assuming beforehand that every interpretable concept must be one-dimensional, BSF changes the featurizer architecture so that multidimensional concepts can themselves become the atomic units of interpretation.

This is perhaps the most interesting lesson of the work. Interpretability methods do not merely reveal whatever structure is already present: their architecture determines **what kinds of structure they are capable of seeing**.


## Formal Foundations

The motivation behind BSF can be summarized through two assumptions: **featurizer–geometry duality** and the **additive mixture of manifolds** model.


<blockquote class="paper-definition" markdown="1">
<div class="paper-definition-title"><span>Definition 1.</span> Featurizer–Geometry Duality</div>

Following the featurizer–geometry duality framework <citation key="projecting-assumptions"></citation>, let $\mathcal{F}$ denote a family of featurizers. Associated with $\mathcal{F}$ is a family of representational geometries

$$
\mathfrak{G}(\mathcal{F}),
$$

consisting of the kinds of structures that the architecture can recover as atomic features.

The **featurizer–geometry duality** is the principle that the architecture of $\mathcal{F}$ determines $\mathfrak{G}(\mathcal{F})$. Therefore, changing the featurizer changes the class of concepts that can naturally be recovered.

Conceptually,

$$
\boxed{
\text{featurizer architecture}
\quad\longleftrightarrow\quad
\text{assumed concept geometry}
}
$$

</blockquote>

A direction-based sparse autoencoder, for example, privileges concepts that can be represented through sparse combinations of individual directions. If the underlying concepts instead have heterogeneous dimensionality or nonlinear internal structure, such an architecture may fragment or obscure them.

The implication for interpretability is that **designing a featurizer is equivalent to specifying a hypothesis about the geometry of neural representations**.

<blockquote class="paper-definition" markdown="1">
<div class="paper-definition-title"><span>Definition 2.</span> Additive Mixture of Manifolds</div>

Following the concept-manifold formulation <citation key="concept-manifolds"></citation>, let

$$
\mathcal{N}_1,\ldots,\mathcal{N}_M
$$

be abstract manifolds with $\dim\mathcal{N}_i\ll d$, immersed into activation space through

$$
\gamma_i:\mathcal{N}_i\rightarrow\mathbb{R}^d,
$$

with images

$$
\mathcal{M}_i
=
\gamma_i(\mathcal{N}_i)
\subseteq
\mathbb{R}^d.
$$

An activation $x$ follows the **Additive Mixture of Manifolds** model if

$$
x
=
\sum_{i\in S}m_i,
\qquad
m_i\in\mathcal{M}_i,
\qquad
S\subseteq[M],
\qquad
|S|\ll M,
$$

where $S$ indexes the small number of factors active in $x$.

Equivalently,

$$
x
\in
\sum_{i\in S}\mathcal{M}_i,
$$

so the activation lies in a sparse **Minkowski sum of manifolds**.

</blockquote>

The model therefore separates two questions:

1. **Which factors are active?**
2. **Where does the representation lie within each active factor?**

The first is naturally a sparsity problem, while the second requires enough dimensions to preserve the internal geometry of each factor.

The block-sparse formulation <citation key="structuring-sparsity"></citation> adds the assumption that each manifold occupies a low-dimensional linear span,

$$
V_g
=
\operatorname{span}(\mathcal{M}_g),
\qquad
\dim(V_g)=k\ll d.
$$

Choosing an orthonormal frame $D_g$ for $V_g$ then allows each manifold contribution to be represented through block coordinates $z_g$. The activation can consequently be written schematically as

$$
x
=
\sum_{g\in S}D_g^\top z_g+\varepsilon,
$$

with only a small number of nonzero blocks.

This is precisely where block sparsity becomes the matched prior: **sparsity is imposed across factors, while dense multidimensional variation is preserved within each active factor**.
