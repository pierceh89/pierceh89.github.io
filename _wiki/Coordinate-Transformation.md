---
layout  : wiki
title   : Coordinate Transformation
summary :
date    : 2020-05-31 13:27:00 +0900
updated : 2020-05-31 13:50:32 +0900
tag     : computer-graphics
toc     : true
public  : true
parent  : [[computer-graphics]]
latex   : true
---
* TOC
{:toc}

This post is about coordinate transformations which I was confused with so much!

Geometrically, coordinate system (or frame) consists of an origin and basis - a set of three vectors. Bases are not necessarily orthonormal, but it is assumed that bases are orthonormal because it is convenient. Let $$ p $$ the origin, $$ \{u,v,w\} $$ bases, and (a,b,c) the coordinates, then we can describe point like this:

$$
p + u * a + v * b + w * c
$$

In canonical coordinates, the origin is $$ o(0, 0, 0) $$ and bases are $$ X(1, 0, 0)^T $$, $$ Y(0, 1, 0)^T $$, $$ Z(0, 0, 1)^T $$. We can use another origin and basis - $$ e, \{U, V, W\} $$. The point $$ P $$ can be described in different coordinates.

$$
P = (P_x, P_y, P_z) \equiv o + P_x*X + P_y*Y + P_z*Z
$$

$$
P = (P_u, P_v, P_w) \equiv e + P_u*U + P_v*V + P_w*W
$$

As $$ e, \{U,V,W\} $$ are described in canonical coordinates too, we can express this relationship using matrix.

$$
\begin{bmatrix} P_x \\ P_y \\ P_z \\ 1 \end{bmatrix} = \begin{bmatrix} 1 & 0 & 0 & e_x \\ 0 & 1 & 0 & e_y \\ 0 & 0 & 1 & e_z \\ 0 & 0 & 0 & 1 \end{bmatrix} * \begin{bmatrix} U_x & V_x & W_x & 0 \\ U_y & V_y & W_y & 0 \\ U_z & V_z & W_z & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix} * \begin{bmatrix} P_u \\ P_v \\ P_w \\ 1 \end{bmatrix} = \begin{bmatrix} U_x & V_x & W_x & e_x \\ U_y & V_y & W_y & e_y \\ U_z & V_z & W_z & e_z \\ 0 & 0 & 0 & 1 \end{bmatrix} * \begin{bmatrix} P_u \\ P_v \\ P_w \\ 1 \end{bmatrix}
$$

This 4x4 matrix is a coordinate transformation matrix **from an arbitrary frame to the canonical coordinate system.**

Then we can obtain transformation matrix in the other direction. The transformation matrix is the inverse of the matrix above - translation and then rotation. When basis vectors are orthonormal, it is simply transposed matrix.

$$
\begin{bmatrix} P_u \\ P_v \\ P_w \\ 1 \end{bmatrix} = \begin{bmatrix} U_x & V_x & W_x & 0 \\ U_y & V_y & W_y & 0 \\ U_z & V_z & W_z & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix}^T * \begin{bmatrix} 1 & 0 & 0 & -e_x \\ 0 & 1 & 0 & -e_y \\ 0 & 0 & 1 & -e_z \\ 0 & 0 & 0 & 1 \end{bmatrix} * \begin{bmatrix} P_x \\ P_y \\ P_z \\ 1 \end{bmatrix}
$$

Now we know how to transform coordinate system! Next we can derive ’gluLookAt’ function using matrices described above. The function changes the world coordinate from the camera coordinate.

{% highlight js %}
gluLookAt( eye, center, up );

//eye - the position of eye point
//center - the reference point
//up - the direction of the up vector

{% endhighlight %}

When we use gluLookAt, three points are given, _eye_ - the position of the camera, _center_ - the viewing direction, and _up_ - the view-up vector. Using three points, we can create the camera frame (coordinate system). The camera is located at _eye_, viewing at -$$ Z^c $$ (OpenGL coordinate), with upward-orientation $$ Y^c $$.

$$
Z^c = normalize(eye-at)
$$

$$
X^c = normalize(Z_c \times up)
$$

$$
Y^c = normalize(Z_c \times X_c)
$$

So we now have a transformation matrix from the world coordinates the camera coordinates. Remember it is from canonical coordinates to a new coordinate.

$$
\begin{bmatrix} X^c_0 & Y^c_0 & Z^c_0 & 0 \\ X^c_1 & Y^c_1 & Z^c_1 & 0 \\ X^c_2 & Y^c_2 & Z^c_2 & 0 \\ 0 & 0 & 0 & 1  \end{bmatrix}^T * \begin{bmatrix} 1 & 0 & 0 & -eye_x \\ 0 & 1 & 0 & -eye_y \\ 0 & 0 & 1 & -eye_z \\ 0 & 0 & 0 & 1 \end{bmatrix} = \begin{bmatrix} X^c_0 & X^c_1 & X^c_2 & 0 \\ Y^c_0 & Y^c_1 & Y^c_2 & 0 \\ Z^c_0 & Z^c_1 & Z^c_2 & 0 \\ 0 & 0 & 0 & 1  \end{bmatrix} * \begin{bmatrix} 1 & 0 & 0 & -eye_x \\ 0 & 1 & 0 & -eye_y \\ 0 & 0 & 1 & -eye_z \\ 0 & 0 & 0 & 1 \end{bmatrix}
$$

Yay! Now we can write our own gluLookAt function.
