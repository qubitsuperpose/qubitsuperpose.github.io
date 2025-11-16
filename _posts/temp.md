{/* cspell:ignore mapsto */}

# Phase estimation procedure

Next, we'll discuss the *phase-estimation procedure*, which is a quantum algorithm for solving the phase estimation problem.

We'll begin with a low-precision warm-up, which explains some of the basic intuition behind the method.
We'll then talk about the *quantum Fourier transform*, which is an important quantum operation used in the phase-estimation procedure, as well as its quantum circuit implementation.
Once we have the quantum Fourier transform in hand, we'll describe the phase-estimation procedure in full generality and analyze its performance.

## Warm-up: approximating phases with low precision

We'll begin with a couple of simple versions of the phase-estimation procedure that provide low-precision solutions to the phase-estimation problem.
This is helpful for explaining the intuition behind the general procedure that we'll see a bit later in the lesson.

### Using the phase kickback

A simple approach to the phase-estimation problem, which allows us to learn something about the value $\theta$ we seek, is based on the *phase kick-back* phenomenon.
As we'll see, this is essentially a single-qubit version of the general phase-estimation procedure to be discussed later in the lesson.

As part of the input to the phase estimation problem, we have a unitary quantum circuit for the operation $U.$
We can use the description of this circuit to create a circuit for a *controlled*-$U$ operation, which can be depicted as this figure suggests (with the operation $U,$ viewed as a quantum gate, on the left and a controlled-$U$ operation on the right).


We can create a quantum circuit for a controlled-$U$ operation by first adding a control qubit to the circuit for $U,$ and then replacing every gate in the circuit for $U$ with a controlled version of that gate — so our one new control qubit effectively controls every single gate in the circuit for $U.$
This requires that we have a controlled version of every gate in our circuit, but we can always build circuits for these controlled operations in case they're not included in our gate set.

Now consider the following circuit, where the input state $\vert\psi\rangle$ of all of the qubits except the top one is the quantum state eigenvector of $U.$


The measurement outcome probabilities for this circuit depend on the eigenvalue of $U$ corresponding to the eigenvector $\vert\psi\rangle.$
Let's analyze the circuit in detail to determine exactly how.



The initial state of the circuit is



and the first Hadamard gate transforms this state to



Next, the controlled-$U$ operation is performed, which results in the state



Using the assumption that $\vert\psi\rangle$ is an eigenvector of $U$ having eigenvalue $\lambda = e^{2\pi i\theta},$
we can alternatively express this state as follows.


Here we observe the phase kickback phenomenon.
It is slightly different this time than it was for Deutsch's algorithm and the Deutsch-Jozsa algorithm because we're not working with a query gate — but the idea is similar.

Finally, the second Hadamard gate is performed. After just a bit of simplification, we obtain this expression for this state.

$$
\vert\pi_3\rangle
= \vert\psi\rangle \otimes \left( \frac{1+ e^{2\pi i \theta}}{2} \vert 0\rangle + \frac{1 - e^{2\pi i \theta}}{2} \vert 1\rangle\right)
$$

The measurement therefore yields the outcomes $0$ and $1$ with these probabilities:



Here's a plot of the probabilities for the two possible outcomes, $0$ and $1,$ as functions of $\theta.$


Naturally, the two probabilities always sum to $1.$
Notice that when $\theta = 0,$ the measurement outcome is always $0,$ and when $\theta = 1/2,$ the measurement outcome is always $1.$
So, although the measurement result doesn't reveal exactly what $\theta$ is, it does provide us with some information about it — and if we were promised that either $\theta = 0$ or $\theta = 1/2,$ we could learn from the circuit which one is correct without error.

Intuitively speaking, we can think of the circuit's measurement outcome as being a guess for $\theta$ to "one bit of accuracy."
In other words, if we were to write $\theta$ in binary notation and round it off to one bit, we'd have a number like this:


The measurement outcome can be viewed as a guess for the bit $a.$
When $\theta$ is neither $0$ nor $1/2,$ there's a nonzero probability that the guess will be wrong — but the
probability of making an error becomes smaller and smaller as we get closer to $0$ or $1/2.$

It's natural to ask what role the two Hadamard gates play in this procedure:

*   The first Hadamard gate sets the control qubit to a uniform superposition of $\vert 0\rangle$ and $\vert 1\rangle,$ so that when the phase kickback occurs, it happens for the $\vert 1\rangle$ state and not the $\vert 0\rangle$ state, creating a *relative* phase difference that affects the measurement outcomes. If we didn't do this and the phase kickback produced a *global* phase, it would have no effect on the probabilities of obtaining different measurement outcomes.

*   The second Hadamard gate allows us to learn something about the number $\theta$ through the phenomenon of *interference*. Prior to the second Hadamard gate, the state of the top qubit is

    $$
    \frac{1}{\sqrt{2}} \vert 0\rangle + \frac{e^{2\pi i \theta}}{\sqrt{2}} \vert 1\rangle,
    $$

    and if we were to measure this state, we would obtain $0$ and $1$ each with probability $1/2,$ telling us nothing about $\theta.$ By performing the second Hadamard gate, however, we cause the number $\theta$ to affect the output probabilities.

### Doubling the phase

The circuit above uses the phase kickback phenomenon to approximate $\theta$ to a single bit of accuracy.
One bit of accuracy may be all we need in some situations — but for factoring we're going to need a lot more accuracy than that.
A natural question is, how can we learn more about $\theta?$

One very simple thing we can do is to replace the controlled-$U$ operation in our circuit with *two copies* of this operation, like in this circuit:

Two copies of a controlled-$U$ operation is equivalent to a controlled-$U^2$ operation.
If $\vert\psi\rangle$ is an eigenvector of $U$ having eigenvalue $\lambda = e^{2\pi i \theta},$ then this state is also an eigenvector of $U^2,$ this time having eigenvalue $\lambda^2 = e^{2\pi i (2\theta)}.$

So, if we run this version of the circuit, we're effectively performing the same computation as before, except that the number $\theta$ is replaced by $2\theta.$
Here's a plot illustrating the output probabilities as $\theta$ ranges from $0$ to $1.$


Doing this can indeed provide us with some additional information about $\theta.$
If the binary representation of $\theta$ is



then doubling $\theta$ effectively shifts the binary point one position to the right:


And because we're equating $\theta = 1$ with $\theta = 0$ as we move around the unit circle, we see that the bit $a_1$ has no influence on our probabilities, and we're effectively obtaining a guess for the *second* bit after the binary point if we round $\theta$ to two bits.
For instance, if we knew in advance that $\theta$ was either $0$ or $1/4,$ then we could fully trust the measurement outcome to tell us which.

It's not immediately clear, though, how this estimation should be reconciled with what we learned from the original (non-doubled) phase kickback circuit to give us the most accurate information possible about $\theta.$
So let's take a step back and consider how to proceed.

### Two-qubit phase estimation

Rather than considering the two options described above separately, let's combine them into a single circuit like so.


The Hadamard gates after the controlled operations have been removed and there are no measurements here yet.
We'll add more to the circuit as we consider our options for learning as much as we can about $\theta.$

If we run this circuit when $\vert\psi\rangle$ is an eigenvector of $U,$ the state of the bottom qubits will remain $\vert\psi\rangle$ throughout the entire circuit, and phases will be "kicked" into the state of the top two qubits.
Let's analyze the circuit carefully, by means of the following figure.


We can write the state $\vert\pi_1\rangle$ like this:


When the first controlled-$U$ operation is performed, the eigenvalue $\lambda = e^{2\pi i\theta}$ gets kicked into the phase when $a_0$ (the top qubit) is equal to $1,$ but not when it's $0.$
So, we can express the resulting state like this:



The second and third controlled-$U$ gates do something similar, except for $a_1$ rather than $a_0,$ and with $\theta$ replaced by $2\theta.$
We can express the resulting state like this:



If we think about the binary string $a_1 a_0$ as representing an integer $x \in \{0,1,2,3\}$ in binary notation, which is $x = 2 a_1 + a_0,$ we can alternatively express this state as follows.



Our goal is to extract as much information about $\theta$ as we can from this state.

At this point we'll consider a special case, where we're promised that $\theta = \frac{y}{4}$ for some integer $y\in\{0,1,2,3\}.$
In other words, we have $\theta\in \{0, 1/4, 1/2, 3/4\},$ so we can express this number exactly using binary notation with two bits, as .$00,$ .$01,$ .$10,$ or .$11.$
In general, $\theta$ might not be one of these four values, but thinking about this special case will help us to figure out how to most effectively extract information about $\theta$ in general.

First we'll define a two-qubit state vector for each possible value $y \in \{0, 1, 2, 3\}.$



After simplifying the exponentials, we can write these vectors as follows.



These vectors are orthogonal: if we choose any pair of them and compute their inner product, we get $0.$
Each one is also a unit vector, so $\{\vert\phi_0\rangle, \vert\phi_1\rangle, \vert\phi_2\rangle, \vert\phi_3\rangle\}$ is an orthonormal basis.
We therefore know right away that there is a measurement that can discriminate them perfectly — meaning that, if we're given one of them but we don't know which, then we can figure out which one it is without error.

To perform such a discrimination with a quantum circuit, we can first define a unitary operation $V$ that transforms standard basis states into the four states listed above.



To write down $V$ as a $4\times 4$ matrix, it's just a matter of taking the columns of $V$ to be the states $\vert\phi_0\rangle,\ldots,\vert\phi_3\rangle.$



This is a special matrix, and it's likely that some readers will have encountered it before:
it's the matrix associated with the $4$-dimensional *discrete Fourier transform*.
In light of this fact, let us call it by the name $\mathrm{QFT}_4$ rather than $V.$
The name $\mathrm{QFT}$ is short for *quantum Fourier transform* — which is essentially just the discrete Fourier transform, viewed as a unitary operation.
We'll discuss the quantum Fourier transform in greater detail and generality shortly.


We can perform the inverse of this operation to go the other way, to transform the states $\vert\phi_0\rangle,\ldots,\vert\phi_3\rangle$ into the standard basis states $\vert 0\rangle,\ldots,\vert 3\rangle.$
If we do this, then we can measure to learn which value $y\in\{0,1,2,3\}$ describes $\theta$ as $\theta = y/4.$
Here's a diagram of a quantum circuit that does this.


To summarize, if we run this circuit when $\theta = y/4$ for $y\in\{0,1,2,3\},$ the state immediately before the measurements take place will be $\vert \psi\rangle \vert y\rangle$ (for $y$ encoded as a two-bit binary string), so the measurements will reveal the value $y$ without error.

This circuit is motivated by the special case that $\theta \in \{0,1/4,1/2,3/4\}$ — but we can run it for any choice of $U$ and $\vert \psi\rangle,$ and hence any value of $\theta,$ that we wish.
Here's a plot of the output probabilities the circuit produces for arbitrary choices of $\theta.$


This is a clear improvement over the single-qubit variant described earlier in the lesson.
It's not perfect — it can give us the wrong answer — but the answer is heavily skewed toward values of $y$ for which $y/4$ is close to $\theta.$
In particular, the most likely outcome always corresponds to the closest value of $y/4$ to $\theta$ (equating $\theta = 0$ and $\theta = 1$ as before), and from the plot it looks like this closest value for $x$ always appears with probability just above $40\%.$
When $\theta$ is exactly halfway between two such values, like $\theta = 0.375$ for instance, the two equally close values of $y$ are equally likely.

### Preparing to generalize to many qubits

Given the improvement we've just obtained by using two control qubits rather than one, in conjunction with the inverse of the $4$-dimensional quantum Fourier transform, it's natural to consider generalizing it further — by adding more control qubits.
When we do this, we obtain the general *phase estimation procedure*.
We'll see how this works shortly, but in order to describe it precisely we're going to need to discuss the quantum Fourier transform in greater generality, to see how it's defined for other dimensions and to see how we can implement it (or its inverse) with a quantum circuit.

## Quantum Fourier transform

The quantum Fourier transform is a unitary operation that can be defined for any positive integer dimension $N.$
In this section, we'll see how this operation is defined and how it can be implemented with a quantum circuit on $m$ qubits with cost $O(m^2)$ when $N = 2^m.$

The matrices that describe the quantum Fourier transform are derived from an analogous operation on $N$-dimensional vectors known as the *discrete Fourier transform.*
This operation can be thought about in different ways.
For instance, we can think about the discrete Fourier transform in purely abstract, mathematical terms as a linear mapping.
Or we can think about it in computational terms, where we're given an $N$-dimensional vector of complex numbers (using binary notation to encode the real and imaginary parts of the entries, let us suppose) and the goal is to calculate the $N$-dimensional vector obtained by applying the discrete Fourier transform.
Our focus will be on third way, which is viewing this transformation as a unitary operation that can be performed on a quantum system.

There's an efficient algorithm for computing the discrete Fourier transform on a given input vector known as the *fast Fourier transform.*
It has applications in signal processing and many other areas, and is considered by many to be one of the most important algorithms ever discovered.
As it turns out, the implementation of the quantum Fourier transform when $N$ is a power of 2 that we'll study is based on precisely the same underlying structure that make the fast Fourier transform possible.

### Definition of the quantum Fourier transform

To define the quantum Fourier transform, we'll first define a complex number $\omega_N,$ for each positive integer $N,$ like this:



This is the number on the complex unit circle we obtain if we start at $1$ and move counter-clockwise by an angle of $2\pi/N$ radians, or a fraction of $1/N$ of the circumference of the circle. Here are a few examples:



Now we can define the $N$-dimensional quantum Fourier transform, which is described by an $N\times N$ matrix whose rows and columns are associated with the standard basis states $\vert 0\rangle,\ldots,\vert N-1\rangle.$
We're only going to need this operation for when $N = 2^m$ is a power of $2$ for phase estimation, but the operation can be defined for any positive integer $N.$



As was already stated, this is the matrix associated with the $N$-dimensional *discrete Fourier transform*.
Often the leading factor of $1/\sqrt{N}$ is not included in the definition of this matrix, but we need to include it to obtain a unitary matrix.

Here's the quantum Fourier transform, written as a matrix, for some small values of $N.$


Notice, in particular, that $\mathrm{QFT}_2$ is another name for a Hadamard operation.

### Unitarity

Let's check that $\mathrm{QFT}_N$ is unitary, for any selection of $N.$
One way to do this is to show that its columns form an orthonormal basis.
We can define a vector corresponding to column number $y,$ starting from $y = 0$ and going up to $y = N-1,$ like this:

$$
\vert\phi_y\rangle = \frac{1}{\sqrt{N}} \sum_{x = 0}^{N-1} \omega_N^{xy} \vert x \rangle.
$$

Taking the inner product between any two of these vectors gives us this expression:

$$
\langle \phi_z \vert \phi_y \rangle = \frac{1}{N} \sum_{x = 0}^{N-1} \omega_N^{x (y - z)}
$$

We can evaluate sums like this using the following formula for the sum of the first $N$ terms of a geometric series.

$$
1 + \alpha + \alpha^2 + \cdots + \alpha^{N-1} =
\begin{cases}
\frac{\alpha^N - 1}{\alpha - 1} & \text{if } \alpha\neq 1\\[2mm]
N & \text{if } \alpha=1
\end{cases}
$$

Specifically, we can use this formula when $\alpha = \omega_N^{y-z}.$
When $y = z,$ we have $\alpha = 1,$ so using the formula and dividing by $N$ gives

$$
\langle \phi_y \vert \phi_y \rangle = 1.
$$

When $y\neq z,$ we have $\alpha \neq 1,$ so the formula reveals this:

$$
\langle \phi_z \vert \phi_y \rangle = \frac{1}{N} \frac{\omega_N^{N(y-z)} - 1}{\omega_N^{y-z} - 1}
= \frac{1}{N} \frac{1 - 1}{\omega_N^{y-z} - 1} = 0.
$$

This happens because $\omega_N^N = e^{2\pi i} = 1,$ so $\omega_N^{N(y-z)} = 1^{y-z} = 1,$ making numerator zero, while the denominator is nonzero because $\omega_N^{y-z} \neq 1.$
Intuitively speaking, what we're doing is summing a bunch of points that are distributed around the unit circle, and they cancel out and leave $0$ when summed.

We have therefore established that $\{\vert\phi_0\rangle,\ldots,\vert\phi_{N-1}\rangle\}$ is an orthonormal set,

$$
\langle \phi_z \vert \phi_y \rangle =
\begin{cases}
1 & y=z\\
0 & y\neq z,
\end{cases}
$$

which reveals that $\mathrm{QFT}_N$ is unitary.

### Controlled-phase gates

To implement the quantum Fourier transform with a quantum circuit, we'll need to make use of *controlled-phase* gates.
Recall that a *phase operation* is a single-qubit unitary operation of the form

$$
P_{\alpha} =
\begin{pmatrix}
1 & 0\\[1mm]
0 & e^{i\alpha}
\end{pmatrix}
$$

for any real number $\alpha.$
A controlled version of this gate has the following matrix:

$$
CP_{\alpha} =
\begin{pmatrix}
1 & 0 & 0 & 0\\[1mm]
0 & 1 & 0 & 0\\[1mm]
0 & 0 & 1 & 0\\[1mm]
0 & 0 & 0 & e^{i\alpha}
\end{pmatrix}
$$

For this controlled gate, it doesn't actually matter which qubit is the control and which is the target because the two possibilities are equivalent.
We can use any of the following symbols to represent this gate in quantum circuit diagrams.

![Quantum circuit diagram representation for controlled-phase gates](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/controlled-phase-gates.svg)

For the third form, the label $\alpha$ is also sometimes placed on the side of the control line or under the lower control when that's convenient.

To perform the quantum Fourier transform when $N = 2^m$ and $m\geq 2,$ we're going to need to perform an operation on $m$ qubits whose action on standard basis states can be described as

$$
\vert y \rangle \vert a \rangle \mapsto \omega_{2^m}^{ay} \vert y \rangle \vert a \rangle,
$$

where $a$ is a bit and $y \in \{0,\ldots,2^{m-1} - 1\}$ is a number encoded in binary notation as a string of $m-1$ bits.
This can be done using controlled-phase gates by generalizing the following example, for which $m=5.$

![Quantum circuit diagram for phase injection](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-injection.svg)

In general, for an arbitrary choice of $m\geq 2,$ the top qubit corresponding to the bit $a$ can be viewed as the control, with the phase gates $P_{\alpha}$ ranging from
$\alpha = \pi/2^{m-1}$ on the qubit corresponding to the least significant bit of $y$ to $\alpha = \frac{\pi}{2}$ on the qubit corresponding to the most significant bit of $y.$
These controlled-phase gates all commute with one another and could be performed in any order.

### Circuit implementation of the QFT

Now we'll see how we can implement the quantum Fourier transform with a circuit when the dimension $N = 2^m$ is a power of $2.$
There are, in fact, multiple ways to implement the quantum Fourier transform, but this is arguably the simplest method known.
Once we know how to implement the quantum Fourier transform with a quantum circuit, it's straightforward to implement its inverse: we can replace each gate with its inverse (or, equivalently, conjugate transpose) and apply the gates in the reverse order.
Every quantum circuit composed of unitary gates alone can be inverted in this way.

The implementation is recursive in nature, so that's how it's most naturally described.
The base case is $m=1,$ in which case the quantum Fourier transform is a Hadamard operation.

To perform the quantum Fourier transform on $m$ qubits when $m \geq 2,$ we can perform the following steps, whose actions we'll describe for standard basis states of the form $\vert x \rangle \vert a\rangle,$ where $x\in\{0,\ldots,2^{m-1} - 1\}$ is an integer encoded as $m-1$ bits using binary notation and $a$ is a single bit.

1.  First apply the $2^{m-1}$-dimensional quantum Fourier transform to the bottom/leftmost $m-1$ qubits to obtain
    this state:

    $$
    \Bigl(\mathrm{QFT}_{2^{m-1}} \vert x \rangle\Bigr) \vert a\rangle
    = \frac{1}{\sqrt{2^{m-1}}} \sum_{y = 0}^{2^{m-1} - 1} \omega_{2^{m-1}}^{xy} \vert y \rangle \vert a \rangle.
    $$

    This is done by recursively applying the method being described for one fewer qubit, using the Hadamard
    operation on a single qubit as the base case.

2.  Use the top/rightmost qubit as a control to inject the phase $\omega_{2^m}^y$ for each standard basis
    state $\vert y\rangle$ of the remaining $m-1$ qubits (as is described above) to obtain this state:

    $$
    \frac{1}{\sqrt{2^{m-1}}} \sum_{y = 0}^{2^{m-1} - 1} \omega_{2^{m-1}}^{xy}
    \omega_{2^m}^{ay} \vert y \rangle \vert a \rangle.
    $$

3.  Perform a Hadamard gate on the top/rightmost qubit to obtain this state:

    $$
    \frac{1}{\sqrt{2^{m}}} \sum_{y = 0}^{2^{m-1} - 1} \sum_{b=0}^1
    (-1)^{ab} \omega_{2^{m-1}}^{xy} \omega_{2^m}^{ay}
    \vert y \rangle \vert b \rangle.
    $$

4.  Permute the order of the qubits so that the least significant bit becomes the most significant bit, with
    all others shifted up/right:

    $$
    \frac{1}{\sqrt{2^{m}}} \sum_{y = 0}^{2^{m-1} - 1} \sum_{b=0}^1
    (-1)^{ab} \omega_{2^{m-1}}^{xy} \omega_{2^m}^{ay}
    \vert b \rangle \vert y \rangle.
    $$

For example, here's the circuit we obtain for $N = 32 = 2^5.$
In this diagram, the qubits are given names that correspond to the standard basis vectors $\vert x\rangle \vert a\rangle$ (for the input) and $\vert b\rangle \vert y\rangle$ (for the output) for clarity.

![Quantum circuit diagram for the 32-dimensional quantum Fourier transform](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/QFT_32.svg)

### Analysis

The key formula we need to verify that the circuit just described implements the $2^m$-dimensional quantum Fourier transform is this one:

$$
(-1)^{ab}
\omega_{2^{m-1}}^{xy}
\omega_{2^m}^{ay}
=
\omega_{2^m}^{(2x+ a)(2^{m-1}b + y)}.
$$

This formula works for any choice of integers $a,$ $b,$ $x,$ and $y,$ but we'll only need it for
$a,b\in\{0,1\}$ and $x,y\in\{0,\ldots,2^{m-1}-1\}.$
It can be checked by expanding the product in the exponent on the right-hand side,

$$
  \omega_{2^m}^{(2x+ a)(2^{m-1}b + y)}
  = \omega_{2^m}^{2^m xb} \omega_{2^m}^{2xy} \omega_{2^m}^{2^{m-1}ab} \omega_{2^m}^{ay}
  = (-1)^{ab} \omega_{2^{m-1}}^{xy} \omega_{2^m}^{ay},
$$

where the second equality makes use of the observation that

$$
\omega_{2^m}^{2^m xb} = \bigl(\omega_{2^m}^{2^m}\bigr)^{xb} = 1^{xb} = 1.
$$

The $2^m$-dimensional quantum Fourier transform is defined as follows for every $u\in\{0,\ldots,2^m - 1\}.$

$$
\mathrm{QFT}_{2^m} \vert u\rangle = \frac{1}{\sqrt{2^m}}
\sum_{v = 0}^{2^m - 1} \omega_{2^m}^{uv} \vert v\rangle
$$

If we write $u$ and $v$ as

$$
\begin{aligned}
u & = 2x + a\\
v & = 2^{m-1}b + y
\end{aligned}
$$

for $a,b\in\{0,1\}$ and $x,y\in\{0,\ldots,2^{m-1} - 1\},$ we obtain

$$
\begin{aligned}
\mathrm{QFT}_{2^m} \vert 2x + a\rangle
& =
\frac{1}{\sqrt{2^m}}
\sum_{y = 0}^{2^{m-1} - 1}
\sum_{b=0}^1
\omega_{2^m}^{(2x+ a)(2^{m-1}b + y)} \vert b 2^{m-1} + y\rangle\\[2mm]
& =
\frac{1}{\sqrt{2^m}}
\sum_{y = 0}^{2^{m-1} - 1}
\sum_{b=0}^1
(-1)^{ab}
\omega_{2^{m-1}}^{xy}
\omega_{2^m}^{ay}
\vert b 2^{m-1} + y\rangle.
\end{aligned}
$$

Finally, by thinking about the standard basis states $\vert x \rangle \vert a\rangle$ and $\vert b \rangle \vert y \rangle$ as binary encodings of integers in the range $\{0,\ldots,2^m-1\},$

$$
\begin{aligned}
\vert x \rangle \vert a\rangle & = \vert 2x + a \rangle\\
\vert b \rangle \vert y \rangle & = \vert 2^{m-1}b + y\rangle,
\end{aligned}
$$

we see that the circuit above implements the required operation.
If this method for performing the quantum Fourier transform seems remarkable, it's because it is:
it's essentially the fast Fourier transform in the form of a quantum circuit.

Finally, let's count how many gates are used in the circuit just described.
The controlled-phase gates aren't in the standard gate set that we discussed in the previous lesson, but to begin we'll ignore this and count each of them as a single gate.

Let's let $s_m$ denote the number of gates we need for each possible choice of $m.$
If $m=1,$ the quantum Fourier transform is just a Hadamard operation, so

$$
s_1 = 1.
$$

If $m\geq 2,$ then in the circuit above we need $s_{m-1}$ gates for the quantum Fourier transform on $m-1$ qubits, plus $m-1$ controlled-phase gates, plus a Hadamard gate, plus $m-1$ swap gates, so

$$
s_m = s_{m-1} + (2m - 1).
$$

We can obtain a closed-form expression by summing:

$$
s_m = \sum_{k = 1}^m (2k - 1) = m^2.
$$

We don't actually need as many swap gates as the method describes.
If we rearrange the gates just a bit, we can push all of the swap gates out to the right and reduce the number of swap gates required to $\lfloor m/2\rfloor.$
Asymptotically speaking this isn't a major improvement: we still obtain circuits with size $O(m^2)$ for performing $\mathrm{QFT}_{2^m}.$

If we wish to implement the quantum Fourier transform using only gates from our standard gate set, we need to either build or approximate each of the controlled-phase gates with gates from our set.
The number required depends on how much accuracy we require, but as a function of $m$ the total cost remains quadratic.

It is, in fact, possible to approximate the quantum Fourier transform quite closely with a sub-quadratic number of gates by using the fact that $P_{\alpha}$ is very close to the identity operation when $\alpha$ is very small — which means that we can simply leave out most of the controlled-phase gates without suffering too much of a loss in terms of accuracy.

## General procedure and analysis

Now we'll examine the phase-estimation procedure in general.
The idea is to extend the two-qubit version of phase estimation that we considered above in the natural way suggested by the following diagram.

![Phase estimation procedure](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-estimation-procedure.svg)

Notice that, for each new control qubit added on the top, we *double* the number of times the unitary operation $U$ is performed.
This is indicated in the diagram by the powers on $U$ for each of the controlled-unitary operations.

The most straightforward way to implement a controlled-$U^k$ operation for some choice of $k$ is simply to repeat a controlled-$U$ operation $k$ times.
If this is indeed the methodology that is used, it must be recognized that the addition of control qubits contributes significantly to the size of the circuit: if we have $m$ control qubits, like the diagram depicts, a total of $2^m - 1$ copies of the controlled-$U$ operation are required.
This means that a significant computational cost is incurred as $m$ is increased — but as we will see, it also leads to a significantly more accurate approximation of $\theta.$

It is important to note, however, that for *some* choices of $U$ it may be possible to create a circuit that implements the operation $U^k$ for large values of $k$ in a more efficient way than simply repeating $k$ times the circuit for $U.$
We'll see a specific example of this in the context of integer factorization later in the lesson, where the efficient algorithm for *modular exponentiation* discussed in the previous lesson comes to the rescue.

Now let us analyze the circuit just described.
The state immediately prior to the inverse quantum Fourier transform looks like this:

$$
\frac{1}{\sqrt{2^m}} \sum_{x = 0}^{2^m - 1} \bigl( U^x \vert\psi\rangle \bigr) \vert x\rangle
= \vert\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum_{x = 0}^{2^m - 1} e^{2\pi i x\theta}  \vert x\rangle.
$$

### A special case

Along similar lines to what we did in the $m=2$ case, we'll first consider the special case that
$\theta = y/2^m$ for $y\in\{0,\ldots,2^m-1\}.$
In this case the state prior to the inverse quantum Fourier transform can alternatively be written like this:

$$
\vert\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum_{x = 0}^{2^m - 1} e^{2\pi i \frac{xy}{2^m}}  \vert x\rangle
= \vert\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum_{x = 0}^{2^m - 1} \omega_{2^m}^{xy}  \vert x\rangle
= \vert\psi\rangle \otimes \mathrm{QFT}_{2^m} \vert y\rangle.
$$

So, when the inverse quantum Fourier transform is applied, the state becomes

$$
\vert\psi\rangle \vert y\rangle
$$

and the measurements reveal $y$ (encoded in binary).

### Bounding the probabilities

For other values of $\theta,$ meaning ones that don't take the form $y/2^m$ for an integer $y,$ the measurement outcomes won't be certain, but we can prove bounds on the probabilities for different outcomes.
Going forward, let's consider an arbitrary choice of $\theta$ satisfying $0\leq \theta < 1.$

After the inverse quantum Fourier transform is performed, the state of the circuit is this:

$$
\vert \psi \rangle \otimes
\frac{1}{2^m} \sum_{y=0}^{2^m - 1} \sum_{x=0}^{2^m-1} e^{2\pi i x (\theta - y/2^m)} \vert y\rangle.
$$

So, when the measurements on the top $m$ qubits are performed, we see each outcome $y$ with probability

$$
p_y =
\left\vert \frac{1}{2^m} \sum_{x=0}^{2^m - 1} e^{2\pi i x (\theta - y/2^m)} \right\vert^2.
$$

To get a better handle on these probabilities, we'll make use of the same formula that we saw before, for the sum of the initial portion of a geometric series.

$$
1 + \alpha + \alpha^2 + \cdots + \alpha^{N-1} =
\begin{cases}
\frac{\alpha^N - 1}{\alpha - 1} & \text{if } \alpha\neq 1\\[2mm]
N & \text{if } \alpha=1
\end{cases}
$$

We can simplify the sum appearing in the formula for $p_y$ by taking $\alpha = e^{2\pi i (\theta - y/2^m)}.$
Here's what we obtain.

$$
\sum_{x=0}^{2^m - 1} e^{2\pi i x (\theta - y/2^m)}
=
\begin{cases}
2^m & \theta = y/2^m\\[2mm]
\frac{e^{2\pi (2^m \theta - y)} - 1}{e^{2\pi (\theta - y/2^m)} - 1}
& \theta\neq y/2^m
\end{cases}
$$

So, in the case that $\theta = y/2^m,$ we find that $p_y = 1$ (as we already knew from considering this special case),
and in the case that $\theta \neq y/2^m,$ we find that

$$
p_y = \frac{1}{2^{2m}} \left\vert \frac{e^{2\pi i (2^m \theta - y)} - 1}{e^{2\pi i (\theta - y/2^m)} - 1}\right\vert^2.
$$

We can learn more about these probabilities by thinking about how arc lengths and chord lengths on the unit circle are related.
Here's a figure that illustrates the relationships we need for any real number $\delta\in \bigl[ -\frac{1}{2},\frac{1}{2}\bigr].$

![Illustration of the relationship between arc and chord lengths](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/arc-and-chord.svg)

First, the chord length (drawn in blue) can't possibly be larger than the arc length (drawn in purple):

$$
\bigl\vert e^{2\pi i \delta} - 1\bigr\vert \leq 2\pi\vert\delta\vert.
$$

Relating these lengths in the other direction, we see that the ratio of the arc length to the chord length is greatest when $\delta = \pm 1/2,$ and in this case the ratio is half the circumference of the circle divided by the diameter, which is $\pi/2.$
Thus, we have

$$
\frac{2\pi\vert\delta\vert}{\bigl\vert e^{2\pi i \delta} - 1\bigr\vert} \leq \frac{\pi}{2},
$$

and so

$$
\bigl\vert e^{2\pi i \delta} - 1\bigr\vert \geq 4\vert\delta\vert.
$$

An analysis based on these relations reveals the following two facts.

1.  Suppose that $\theta$ is a real number and $y\in \{0,\ldots,2^m-1\}$ satisfies

    $$
    \Bigl\vert \theta - \frac{y}{2^m}\Bigr\vert \leq 2^{-(m+1)}.
    $$

    This means that $y/2^m$ is either the best $m$-bit approximation to $\theta,$ or it's exactly halfway between $y/2^m$ and either $(y-1)/2^m$ or $(y+1)/2^m,$ so it's one of the two best approximations to $\theta.$

    We'll prove that $p_y$ has to be pretty large in this case.
    By the assumption we're considering, it follows that $\vert 2^m \theta - y \vert \leq 1/2,$ so we can use the second observation above relating arc and chord lengths to conclude that

    $$
    \left\vert e^{2\pi i (2^m \theta - y)} - 1\right\vert
    \geq 4 \vert 2^m \theta - y \vert = 4 \cdot 2^m \cdot \Bigl\vert \theta - \frac{y}{2^m}\Bigr\vert.
    $$

    We can also use the first observation about arc and chord lengths to conclude that

    $$
    \left\vert e^{2\pi i (\theta - y/2^m)} - 1\right\vert \leq 2\pi \Bigl\vert \theta - \frac{y}{2^m}\Bigr\vert.
    $$

    Putting these two inequalities to use on $p_y$ reveals

    $$
    p_y \geq \frac{1}{2^{2m}} \frac{16 \cdot 2^{2m}}{4 \pi^2} = \frac{4}{\pi^2} \approx 0.405.
    $$

    This explains our observation that the best outcome occurs with probability greater than $40\%$ in the $m=2$ version of phase estimation discussed earlier.
    It's not really 40%, it's $4/\pi^2,$ and in fact this bound holds for every choice of $m.$

2.  Now suppose that $y\in \{0,\ldots,2^m-1\}$ satisfies

    $$
    2^{-m} \leq \Bigl\vert \theta - \frac{y}{2^m}\Bigr\vert \leq \frac{1}{2}.
    $$

    This means that there's a better approximation $z/2^m$ to $\theta$ in between $\theta$ and $y/2^m.$

    This time we'll prove that $p_y$ can't be too big.
    We can start with the simple observation that

    $$
    \left\vert e^{2\pi i (2^m \theta - y)} - 1\right\vert \leq 2,
    $$

    which follows from the fact that any two points on the unit circle can differ in absolute value by at most $2.$

    We can also use the second observation about arc and chord lengths from above, this time working with the denominator of $p_y$ rather than the numerator, to conclude

    $$
    \left\vert e^{2\pi i (\theta - y/2^m)} - 1\right\vert \geq  4\Bigl\vert \theta - \frac{y}{2^m}\Bigr\vert
    \geq 4 \cdot 2^{-m}.
    $$

    Putting the two inequalities together reveals

    $$
    p_y \leq \frac{1}{2^{2m}} \frac{4}{16 \cdot 2^{-2m}} = \frac{1}{4}.
    $$

    Note that, while this bound is good enough for our purposes, it is fairly crude — the probability is usually much lower than $1/4.$

The important take-away from this analysis is that very close approximations to $\theta$ are likely to occur — we'll get a best $m$-bit approximation with probability greater than $40\%$ — whereas approximations off by more than $2^{-m}$ are less likely to occur, with probability upper bounded by $25\%.$

Given these guarantees, it is possible to boost our confidence by repeating the phase estimation procedure several times, to gather statistical evidence about $\theta.$
It is important to note that the state $\vert\psi\rangle$ of the bottom collection of qubits is unchanged by the phase estimation procedure, so it can be used to run the procedure as many times as we like.
In particular, each time we run the circuit, we get a best $m$-bit approximation to $\theta$ with probability greater than $40\%,$ while the probability of being off by more than $2^{-m}$ is bounded by $25\%.$
If we run the circuit several times and take the most commonly appearing outcome of the runs, it's therefore exceedingly likely that the outcome that appears most commonly will not be one that occurs at most $25\%$ of the time.
As a result, we'll be very likely to obtain an approximation $y/2^m$ that's within $1/2^m$ of the value $\theta.$
Indeed, the unlikely chance that we're off by more than $1/2^m$ decreases exponentially in the number of times the procedure is run.

Here are two plots showing the probabilities for three consecutive values for $y$ when $m = 3$ and $m=4$ as functions of $\theta.$
(Only three outcomes are shown for clarity. Probabilities for other outcomes are obtained by cyclically shifting the same underlying function.)

![Plot showing outcome probabilities for three-qubit phase estimation](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/three-qubit-probabilities.svg)

![Plot showing outcome probabilities for four-qubit phase estimation](/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/four-qubit-probabilities.svg)


----
-----
제공해주신 소스 내용 중 코드 및 수식을 제외한 부분을 한국어로 번역하고, 각 내용을 출처와 함께 제시합니다.

# Hello world

이 예제는 두 부분으로 구성되어 있습니다. 먼저 간단한 양자 프로그램을 생성하고 양자 처리 장치(QPU)에서 실행합니다. 실제 양자 연구에는 훨씬 더 강력한 프로그램이 필요하기 때문에, 두 번째 섹션(\[Scale to large numbers of qubits](#scale-to-large-numbers-of-qubits))에서는 간단한 프로그램을 유틸리티 수준으로 확장할 것입니다.

## 시작하기 전에

아직 설치하지 않았다면, \[Install and set up](/docs/guides/install-qiskit) 지침을 따르십시오. 여기에는 **IBM Cloud®를 사용하도록 설정하는** 단계도 포함됩니다.

IBM Quantum® 플랫폼 문서에 있는 코드 예제는 **Jupyter** 노트북을 사용하여 개발되었습니다. 예제를 따라 하려면 Jupyter 노트북을 **로컬에서** 실행하거나 **온라인 환경**에서 실행할 수 있는 환경을 설정하는 것이 좋습니다. 권장되는 추가 시각화 지원(`'qiskit[visualization]'`)을 반드시 설치하십시오. 또한 이 예제의 두 번째 부분에서는 `matplotlib` 패키지가 필요합니다.

양자 컴퓨팅에 대해 전반적으로 배우려면 IBM Quantum Learning의 **\[양자 정보 기초 과정](/learning/courses/basics-of-quantum-information)**을 방문하십시오. IBM®은 양자 컴퓨팅의 책임 있는 개발에 전념하고 있습니다. IBM의 책임 있는 양자에 대해 자세히 알아보고 **\[책임 있는 양자 컴퓨팅 및 포괄적 기술](/docs/responsible-quantum-computing)** 항목에서 책임 있는 양자 원칙을 검토하십시오.

## 간단한 양자 프로그램 생성 및 실행

Qiskit 패턴을 사용하여 양자 프로그램을 작성하는 네 가지 단계는 다음과 같습니다:

1. **문제를 양자 고유 형식으로 매핑**합니다.
2. **회로와 연산자를 최적화**합니다.
3. **양자 프리미티브 함수를 사용하여 실행**합니다.
4. **결과를 분석**합니다.

### 단계 1. 문제를 양자 고유 형식으로 매핑

양자 프로그램에서 **양자 회로**는 양자 명령을 나타내는 고유 형식이며, **연산자**는 측정할 관측량(observables)을 나타냅니다. 회로를 만들 때, 일반적으로 새 \[`QuantumCircuit`](/docs/api/qiskit/qiskit.circuit.QuantumCircuit#quantumcircuit-class) 객체를 생성한 다음, 순서대로 명령을 추가합니다.

다음 코드 셀은 두 큐비트가 서로 완전히 얽혀 있는 상태인 **벨 상태(Bell state)**를 생성하는 회로를 만듭니다.

Qiskit SDK는 $n$번째 숫자가 $1 \ll n$ 또는 $2^n$ 값을 가지는 LSb 0 비트 번호 지정을 사용합니다. 자세한 내용은 \[Bit-ordering in the Qiskit SDK](/docs/guides/bit-ordering) 항목을 참조하십시오.

사용 가능한 모든 작업에 대해서는 문서의 \[`QuantumCircuit`](/docs/api/qiskit/qiskit.circuit.QuantumCircuit#quantumcircuit-class)를 참조하십시오.

양자 회로를 만들 때 실행 후 **어떤 유형의 데이터를 반환**받을지 고려해야 합니다. Qiskit은 데이터를 반환하는 두 가지 방법을 제공합니다: 측정하려는 큐비트 세트에 대한 **확률 분포**를 얻거나, **관측량의 기댓값**을 얻을 수 있습니다. \[Qiskit primitives](/docs/guides/get-started-with-primitives)를 사용하여 이 두 가지 방법 중 하나로 회로를 측정하도록 워크로드를 준비하십시오 (자세한 내용은 \[Step 3](#step-3-execute-using-the-quantum-primitives)에서 설명됩니다).

이 예제는 **`qiskit.quantum_info`** 서브 모듈을 사용하여 기댓값을 측정하는데, 이는 양자 상태를 변경하는 동작 또는 프로세스를 나타내는 데 사용되는 수학적 객체인 **연산자**를 사용하여 지정됩니다. 다음 코드 셀은 6개의 2큐비트 Pauli 연산자(`IZ`, `IX`, `ZI`, `XI`, `ZZ`, `XX`)를 생성합니다.

여기서 `ZZ`와 같은 연산자는 텐서곱 $Z\otimes Z$의 약어입니다. 이는 큐비트 1과 큐비트 0에서 Z를 함께 측정하고 큐비트 1과 큐비트 0 사이의 **상관 관계**에 대한 정보를 얻는 것을 의미합니다. 이러한 기댓값은 일반적으로 $\langle Z_1 Z_0 \rangle$로도 표기됩니다. 상태가 얽혀 있는 경우, $\langle Z_1 Z_0 \rangle$의 측정값은 $\langle I_1 \otimes Z_0 \rangle \langle Z_1 \otimes I_0 \rangle$의 측정값과 달라야 합니다. 위에서 설명한 회로로 생성된 특정 얽힌 상태의 경우, $\langle Z_1 Z_0 \rangle$의 측정값은 1이어야 하고 $\langle I_1 \otimes Z_0 \rangle \langle Z_1 \otimes I_0 \rangle$의 측정값은 0이어야 합니다.

### 단계 2. 회로 및 연산자 최적화

장치에서 회로를 실행할 때, 회로에 포함된 명령어 세트를 최적화하고 **전체 깊이** (대략 명령어의 수)를 최소화하는 것이 중요합니다. 이는 오류와 노이즈의 영향을 줄여 가능한 최상의 결과를 얻도록 보장합니다. 또한, 회로의 명령어는 백엔드 장치의 **\[Instruction Set Architecture (ISA)](/docs/guides/transpile#instruction-set-architecture)**를 준수해야 하며, 장치의 기본 게이트와 큐비트 연결성을 고려해야 합니다.

다음 코드는 작업을 제출할 실제 장치를 인스턴스화하고, 해당 백엔드의 ISA와 일치하도록 회로와 관측량을 변환합니다. 이 작업을 수행하려면 자격 증명(credentials)을 이미 저장했어야 합니다.

### 단계 3. 양자 프리미티브를 사용한 실행

양자 컴퓨터는 무작위 결과를 생성할 수 있으므로, 일반적으로 회로를 여러 번 실행하여 출력 샘플을 수집합니다. `Estimator` 클래스를 사용하여 관측량의 값을 추정할 수 있습니다. `Estimator`는 두 가지 **프리미티브** 중 하나이며; 다른 하나는 양자 컴퓨터에서 데이터를 얻는 데 사용될 수 있는 `Sampler`입니다. 이 객체들은 **프리미티브 통합 블록(PUB)**을 사용하여 회로, 관측량, 그리고 매개변수(해당하는 경우)의 선택을 실행하는 `run()` 메서드를 가지고 있습니다.

작업이 제출된 후, 현재 Python 인스턴스 내에서 작업이 완료될 때까지 기다리거나, `job_id`를 사용하여 나중에 데이터를 검색할 수 있습니다 (자세한 내용은 \[섹션 on retrieving jobs](/docs/guides/monitor-job#retrieve-job-results-at-a-later-time) 참조). 작업이 완료되면 작업의 `result()` 속성을 통해 출력을 검토하십시오.

이것은 전체 제출의 결과입니다. 하나의 Pub을 제출했으므로, 여기에는 하나의 내부 결과 (및 자체 메타데이터)가 포함됩니다. 이것은 6개의 관측량을 가진 단일 Pub의 결과이므로, 6개 모두에 대한 정보를 포함합니다.

양자 프로그램을 실제 장치에서 실행할 때, 워크로드는 실행되기 전에 대기열에서 기다려야 합니다. 시간을 절약하기 위해, 대신 다음 코드를 사용하여 Qiskit Runtime 로컬 테스트 모드와 함께 [`fake_provider`](../api/qiskit-ibm-runtime/fake-provider)에서 이 작은 워크로드를 실행할 수 있습니다. 이는 작은 회로에 대해서만 가능하다는 점에 유의하십시오. 다음 섹션에서 규모를 확장할 때는 실제 장치를 사용해야 합니다.

(시뮬레이터 코드 블록에 대한 설명:) 이것은 전체 제출의 결과입니다. 하나의 Pub을 제출했으므로, 여기에는 하나의 내부 결과 (및 자체 메타데이터)가 포함됩니다. 이것은 5개의 관측량을 가진 단일 Pub의 결과이므로, 5개 모두에 대한 정보를 포함합니다.

### 단계 4. 결과 분석



(플롯 분석) 큐비트 0과 1의 경우, X와 Z의 독립적인 기댓값은 모두 0인 반면, **상관 관계**(`XX` 및 `ZZ`)는 1이라는 점에 주목하십시오. 이는 **양자 얽힘(quantum entanglement)의 특징**입니다.

## 2. 대규모 큐비트로 확장

양자 컴퓨팅에서 **유틸리티 규모의 작업**은 이 분야의 발전에 매우 중요합니다. 이러한 작업은 100개 이상의 큐비트와 1000개 이상의 게이트를 사용할 수 있는 회로를 다루는 등 훨씬 더 큰 규모로 계산을 수행해야 합니다. 이 예제는 **100-큐비트 GHZ 상태**를 생성하고 분석하여 IBM® QPU에서 유틸리티 규모의 작업을 수행하는 방법을 보여줍니다. Qiskit 패턴 워크플로를 사용하며, 각 큐비트에 대한 기댓값 $\langle Z_0 Z_i \rangle$를 측정하는 것으로 끝납니다.

### 단계 1. 문제 매핑

$n$-큐비트 GHZ 상태(본질적으로 확장된 벨 상태)를 준비하는 `QuantumCircuit`를 반환하는 함수를 작성한 다음, 이 함수를 사용하여 100-큐비트 GHZ 상태를 준비하고 측정할 관측량을 수집합니다.

다음으로, 관심 있는 연산자로 매핑합니다. 이 예제는 큐비트 사이의 `ZZ` 연산자를 사용하여 큐비트가 서로 멀어짐에 따라 동작이 어떻게 되는지 조사합니다. 멀리 떨어진 큐비트 간에 점점 더 부정확해지는 (손상된) 기댓값은 존재하는 노이즈 수준을 나타낼 것입니다.

### 단계 2. 양자 하드웨어 실행을 위한 문제 최적화

다음 코드는 백엔드의 ISA와 일치하도록 회로와 관측량을 변환합니다. 이 작업을 수행하려면 자격 증명을 이미 저장했어야 합니다.

### 단계 3. 하드웨어에서 실행

작업을 제출하고 **동역학적 디커플링**이라고 불리는 오류 감소 기술을 사용하여 오류 억제를 활성화하십시오. **복원력 수준(resilience level)**은 오류에 대해 구축할 복원력의 정도를 지정합니다. 수준이 높을수록 처리 시간이 길어지는 대신 더 정확한 결과를 생성합니다. 다음 코드에 설정된 옵션에 대한 자세한 설명은 \[Configure error mitigation for Qiskit Runtime](/docs/guides/configure-error-mitigation)을 참조하십시오.

### 단계 4. 결과 후처리

작업이 완료된 후, 결과를 플로팅하고 이상적인 시뮬레이션에서는 모든 $\langle Z_0 Z_i \rangle$가 1이어야 함에도 불구하고, **$i$가 증가함에 따라 $\langle Z_0 Z_i \rangle$가 감소**한다는 점에 주목하십시오.

이전 플롯은 큐비트 간 거리가 증가함에 따라 **노이즈 존재로 인해 신호가 감쇠**한다는 것을 보여줍니다.

## 다음 단계

- **회로를 구축하는 방법**에 대해 더 자세히 알아보십시오.
- **VQE를 사용한 하이젠베르크 체인의 바닥 상태 에너지 추정** 튜토리얼을 시도해 보십시오.


제시된 파일 내용을 한국어로 번역했습니다. 요청하신 대로 소스 코드, 수식, 코드 내 주석은 번역에서 제외하였습니다.

***

# CHSH 부등식

*사용량 추정치: Heron r2 프로세서에서 2분 (참고: 이는 추정치일 뿐입니다. 실제 실행 시간은 다를 수 있습니다.)*

## 배경

이 튜토리얼에서는 **Estimator 기본 연산(primitive)을 사용하여 CHSH 부등식 위반을 입증하는 실험을 양자 컴퓨터에서 실행**할 것입니다.

Clauser, Horne, Shimony, Holt의 이름을 따서 명명된 CHSH 부등식은 **벨의 정리(Bell's theorem, 1969)를 실험적으로 입증**하는 데 사용됩니다. 이 정리는 국소 은닉 변수 이론(local hidden variable theories)이 양자 역학의 얽힘(entanglement)의 일부 결과를 설명할 수 없다고 주장합니다.

**CHSH 부등식의 위반은 양자 역학이 국소 은닉 변수 이론과 양립할 수 없음**을 보여주는 데 사용됩니다. 이는 양자 역학의 기초를 이해하는 데 중요한 실험입니다.

2022년 노벨 물리학상은 Alain Aspect, John Clauser 및 Anton Zeilinger에게 양자 정보 과학에서의 선구적인 작업, 특히 **벨 부등식 위반을 입증하는 얽힌 광자(entangled photons)를 사용한 실험**으로 인해 부분적으로 수여되었습니다.

## 요구 사항

이 튜토리얼을 시작하기 전에 다음 사항이 설치되어 있는지 확인하십시오:

*   Qiskit SDK 1.0 이상
*   Qiskit Runtime (`pip install qiskit-ibm-runtime`) 0.22 이상
*   시각화 지원 (`'qiskit[visualization]'`)

## 설정

## 단계 1: 고전적 입력을 양자 문제에 매핑

이 실험을 위해 우리는 **얽힌 쌍(entangled pair)을 생성**하고, 각 큐비트를 두 가지 다른 기저에서 측정할 것입니다. 첫 번째 큐비트의 기저를 $A$와 $a$로, 두 번째 큐비트의 기저를 $B$와 $b$로 명명할 것입니다. 이를 통해 CHSH 값 $S_1$을 계산할 수 있습니다.

각 관측 가능량(observable)은 $+1$ 또는 $-1$입니다. 분명히 항 $B\pm b$ 중 하나는 $0$이어야 하고, 다른 하나는 $\pm 2$이어야 합니다. 따라서 $S_1 = \pm 2$입니다. $S_1$의 평균값은 부등식을 만족해야 합니다.

$S_1$을 $A, a, B, b$ 항으로 전개합니다.

또 다른 CHSH 값 $S_2$를 정의할 수 있습니다. 이는 또 다른 부등식으로 이어집니다.

양자 역학이 국소 은닉 변수 이론으로 설명될 수 있다면, 앞서 언급된 부등식들은 반드시 참이어야 합니다. 하지만 이 튜토리얼에서 입증되듯이, 이 부등식들은 **양자 컴퓨터에서 위반될 수** 있습니다. 그러므로 양자 역학은 국소 은닉 변수 이론과 양립할 수 없습니다.

더 많은 이론을 배우고 싶다면, John Watrous와 함께 **[Entanglement in Action](/learning/courses/basics-of-quantum-information/entanglement-in-action/chsh-game)**을 살펴보십시오.

양자 컴퓨터에서 벨 상태 $|\Phi^+\rangle = \frac{|00\rangle + |11\rangle}{\sqrt{2}}$를 생성함으로써 두 큐비트 사이에 얽힌 쌍을 생성할 것입니다. Estimator 기본 연산을 사용하여, 두 CHSH 값 $\langle S_1\rangle$과 $\langle S_2\rangle$의 기댓값을 계산하는 데 필요한 기댓값들($\langle AB \rangle, \langle Ab \rangle, \langle aB \rangle$, 그리고 $\langle ab \rangle$)을 직접 얻을 수 있습니다. Estimator 기본 연산이 도입되기 전에는 측정 결과(measurement outcomes)로부터 기댓값을 구성해야 했습니다.

두 번째 큐비트는 $Z$ 기저와 $X$ 기저에서 측정할 것입니다. 첫 번째 큐비트도 직교 기저(orthogonal bases)에서 측정되지만, 두 번째 큐비트에 대한 각도를 $0$에서 $2\pi$ 사이로 스위프(sweep)할 것입니다. 보시다시피, Estimator 기본 연산은 매개변수화된 회로(parameterized circuits)를 실행하는 것을 매우 쉽게 만듭니다. 일련의 CHSH 회로를 만드는 대신, 측정 각도를 지정하는 매개변수를 가진 **하나의 CHSH 회로**와 해당 매개변수에 대한 일련의 위상 값만 만들면 됩니다.

마지막으로, 결과를 분석하고 이를 측정 각도에 대해 플롯할 것입니다. 특정 측정 각도 범위에 대해 CHSH 값의 기댓값 $|\langle S_1\rangle| > 2$ 또는 $|\langle S_2\rangle| > 2$인 것을 보게 될 것이며, 이는 **CHSH 부등식의 위반을 입증**합니다.

> 'ibm\_kingston'

### 매개변수화된 CHSH 회로 생성

먼저, 매개변수 $\theta$를 사용하여 회로를 작성하며, 이를 'theta'라고 부릅니다. [`Estimator` 기본 연산](https://docs.quantum-computing.ibm.com/api/qiskit-ibm-runtime/qiskit_ibm_runtime.EstimatorV2)은 **관측 가능량의 기댓값을 직접 제공**하여 회로 구축 및 출력 분석을 엄청나게 단순화할 수 있습니다. 특히 잡음이 있는 시스템에서의 근시일(near-term) 응용 분야에서 관심 있는 많은 문제들은 기댓값의 관점에서 공식화될 수 있습니다. `Estimator` (V2) 기본 연산은 제공된 관측 가능량에 기반하여 측정 기저를 자동으로 변경할 수 있습니다.

### 나중에 할당할 위상 값 목록 생성

매개변수화된 CHSH 회로를 생성한 후, 다음 단계에서 회로에 할당될 위상 값 목록을 생성할 것입니다. 다음 코드를 사용하여 $0$부터 $2\pi$까지 동일한 간격으로 배열된 21개의 위상 값, 즉 $0, 0.1\pi, 0.2\pi, \dots, 1.9\pi, 2\pi$ 목록을 생성할 수 있습니다.

### 관측 가능량

이제 기댓값을 계산할 관측 가능량이 필요합니다. 이 경우, 우리는 각 큐비트에 대한 직교 기저를 보고 있으며, 첫 번째 큐비트에 대한 매개변수화된 $Y$ 회전을 통해 두 번째 큐비트 기저에 대해 측정 기저를 거의 연속적으로 스위프하도록 합니다. 따라서 우리는 관측 가능량 $ZZ, ZX, XZ, XX$를 선택할 것입니다.

## 단계 2: 양자 하드웨어 실행을 위한 문제 최적화

전체 작업 실행 시간을 줄이기 위해, V2 기본 연산은 대상 시스템이 지원하는 명령어 및 연결성(instruction set architecture, ISA 회로 및 관측 가능량이라고 함)을 준수하는 회로와 관측 가능량만 허용합니다.

### ISA 회로

### ISA 관측 가능량

마찬가지로, [`Runtime Estimator V2`](/docs/api/qiskit-ibm-runtime/estimator-v2#run)로 작업을 실행하기 전에 관측 가능량을 백엔드 호환 가능하도록 변환해야 합니다. 이 변환은 `SparsePauliOp` 객체의 `apply_layout` 메서드를 사용하여 수행할 수 있습니다.

## 단계 3: Qiskit 기본 연산을 사용한 실행

전체 실험을 [`Estimator`](https://docs.quantum-computing.ibm.com/api/qiskit-ibm-runtime/qiskit_ibm_runtime.EstimatorV2)에 대한 한 번의 호출로 실행하기 위해, 기댓값을 계산하기 위해 **[Qiskit Runtime `Estimator`](/docs/api/qiskit-ibm-runtime/estimator-v2) 기본 연산을 생성**할 수 있습니다. `EstimatorV2.run()` 메서드는 '기본 통합 블록(primitive unified blocs, PUBs)'의 반복 가능 객체를 받습니다. 각 PUB는 `(회로, 관측 가능량, 매개변수 값: 선택 사항, 정밀도: 선택 사항)` 형식의 반복 가능 객체입니다.

## 단계 4: 후처리 및 원하는 고전적 형식으로 결과 반환

Estimator는 두 관측 가능량 $\langle ZZ \rangle - \langle ZX \rangle + \langle XZ \rangle + \langle XX \rangle$ 와 $\langle ZZ \rangle + \langle ZX \rangle - \langle XZ \rangle + \langle XX \rangle$ 모두에 대한 기댓값을 반환합니다.

그림에서 선과 회색 영역은 경계를 나타냅니다. 가장 바깥쪽의 (대시-점선) 선은 양자 경계($\pm 2$)를 나타내는 반면, 안쪽의 (점선) 선은 고전적 경계($\pm 2\sqrt{2}$)를 나타냅니다. **[참고: 소스의 텍스트에서는 양자 경계를 $\pm 2$, 고전적 경계를 $\pm 2\sqrt{2}$로 명시하고 있는데, 이는 일반적인 CHSH 이론과 그림의 코드 상의 값 ($\pm 2$가 고전적, $\pm 2\sqrt{2}$가 양자 경계)과 상충됩니다. 번역은 소스 원문 텍스트를 따랐습니다.]**

CHSH 증인(witness) 값이 고전적 경계를 초과하는 영역이 있음을 볼 수 있습니다. 축하합니다! 실제 양자 시스템에서 CHSH 부등식의 위반을 성공적으로 입증했습니다!

