## Rotating a 3D Vector
To people who are new to 3D graphics, the concept of rotating 3D points can be very intimidating.
This post is a guide to easily rotate 3D vectors.

You may have heard of yaw, pitch, and roll. These are formally called the Euler angles,
and this is probably the first thing that came to your mind when you thought of rotating vectors.

While using this system does work, it has a few caveats.

### Problems with Euler angles
Here are some problems with using Euler angles to rotate 3D vectors.

1. Slow computational speed

By definition, an Euler angle notation has three axes to rotate around.
This also means the computer needs to to 3 sequential computations to rotate a vector.
Although the computations don't involve sqaure roots, this is still slower compared to other methods.

2. Gimbal lock

[Gimbal lock](https://en.wikipedia.org/wiki/Gimbal_lock) is a phenomenon where many developers give up on Euler angles.
Wikipedia does a much better job of explaining it, so I won't even try.

3. Ambiguity

This may not even seem like a problem at first,
but when you'll probably lose all faith in Euler angles once you here the argument.

First of all, what does 45 degrees towards positive yaw mean?
A physicist would probably say it means a counter-clockwise rotation along the Y axis.
A gamer might say it means a clockwise rotation along the Y axis.
Someone who has lost their bearings could say it means some rotaion along an arbitrary unit vector.

Now combine all these people and make a team of developers. Time for chaos!

The truth is, even in the world of physics, there is no universal definition for what yaw, pitch, and roll mean.
There is a widely accepted convention called the right-hand rule, but even that is arbitrary in itself.

### A better alternative: Axis/Angle
So people came up with a better system called axis/angle representation.
While Euler angles use 3 axes to denote rotation, the axis/angle system uses only one.
Angle is **usually** defined as the counter-clockwise rotation in radians.

An axis/angle representation is composed of a unit vector which denotes the axis,
and a scalar value which denotes the amount of rotation along that axis.

Any three-dimensional rotation can be denoted using an axis/angle represenation.
When the axis has no magnitude, it is considered **rotationless**.

But still, rotating by an axis/angle notation is very mathematically confusing.
So is there a better alternative?

Turns out we have to go back in time, instead of trying to go forward.

### The most advanced system yet: Quaternions
[Quaternions](https://en.wikipedia.org/wiki/Quaternion) were developed by an Irish mathematician
William Rowan Hamilton in 1843, as a way of doing something mathematical. (I don't know exactly, and it doesn't really matter)

What really matters is how we can use quaternions to elegantly describe the rotation of 3D vectors.
There are no matrices involved, and all we need to do is perform a bunch of cross product and dot products on a few vectors.

### So how to I rotate a vector?
Alright. Enough explaining, let's start coding. I'll be using Java, but the language really doesn't matter.

First, define a vector. Name it whatever you want. The common names for vector classes are `Vector` and `Vector3`.
Immutability is optional, but I personally do recommend it.

    // If you're in a higher version of Java, you can also use records
    // I only defined the necessary methods required for vector rotation
    public class Vector {
        // This is used multiple times
        public static final Vector ZERO = new Vector(0, 0, 0);

        // A unit vector to test things out with
        public static final Vector POSITIVE_Y = new Vector(0, 1, 0);

        public Vector(double x, double y, double z) {
            this.x = x;
            this.y = y;
            this.z = z;
        }

        // Three scalar components
        private final double x;
        private final double y;
        private final double z;

        // Getters
        public double getX() {return x;}
        public double getY() {return y;}
        public double getZ() {return z;}

        // Setters
        public Vector setX(double x) {return new Vector(x, y, z);}
        public Vector setY(double y) {return new Vector(x, y, z);}
        public Vector setZ(double z) {return new Vector(x, y, z);}

        // We can use the magnitude to check if the vector was properly rotated
        public double getMagnitude() {
            return Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2) + Math.pos(z, 2));
        }

        public Vector add(Vector v) {
            return new Vector(x + v.x, y + v.y, z + v.z);
        }

        public Vector multiply(double s) {
            return new Vector(x * s, y * s, z * s);
        }

        public Vector divide(double s) {
            if (s == 0) throw new ArithmeticException("Cannot divide by zero.");
            return multiply(1 / s);
        }

        public double dot(Vector v) {
            return x * v.x + y * v.y + z * v.z;
        }

        public Vector cross(Vector v) {
            return new Vector(
                    y * v.z - z * v.y,
                    z * v.x - x * v.z,
                    x * v.y - y * v.x
            );
        }

        // Some developers call this method normalize()
        public Vector toUnitVector() {
            final double m = getMagnitude();
            if(m == 0) return ZERO;

            return new Vector(x / m, y / m, z / m);
        }

        public boolean equals(Object obj) {
            if (obj == null) return false;
            if (!(obj instanceof Vector v)) return false;
            return x == v.x && y == v.y && z == v.z;
        }

        @Override
        public String toString() {
            return "Vector{" +
                    "x=" + x +
                    ", y=" + y +
                    ", z=" + z +
                    '}';
        }

        // Make the class abstract or comment these out for now
        // We'll define these later

        public Quaternion toPureQuaternion();
        public Vector rotate(Quaternion rq);
    }

Now, we need a quaternion.

    // Although vectors and quaternions have 3 common values, extending Vector is not recommended
    // I've personally tried it, and it causes a lot of issues later on
    public class Quaternion {
        // This represents no rotation
        public static final Quaternion IDENTITY_QUATERNION = new Quaternion(1, 0, 0, 0);

        // Gets a rotation quaternion from axis/angle notation
        public static Quaternion fromAxisAngle(Vector axis, double angle) {
            final Vector unit = axis.toUnitVector();
            if (unit.equals(Vector.ZERO)) {
                return IDENTITY_QUATERNION;
            }

            return new Quaternion(Math.cos(angle / 2), unit.multiply(Math.sin(angle / 2)));
        }

        // Defines a quaternion from four scalar values
        public Quaternion(double w, double x, double y, double z) {
            this.w = w;
            this.x = x;
            this.y = y;
            this.z = z;
        }

        // Defines a quaternion from a scalar-vector pair
        public Quaternion(double s, Vector v) {
            this(s, v.getX(), v.getY(), v.getZ());
        }

        private final double w;
        private final double x;
        private final double y;
        private final double z;

        // Getters
        public double getW() {return w;}
        public double getX() {return x;}
        public double getY() {return y;}
        public double getZ() {return z;}
        public Vector getVectorPart() {return new Vector(x, y, z);}

        // Scaling
        // This is useful for scaling rotations up or down
        public Quaternion scale(double s) {
            // Scaling identity quaternions is unnecessary
            if (w == 1) return IDENTITY_QUATERNION;

            final double acos = Math.acos(w);
            return new Quaternion(
                Math.cos(acos * s),
                getVectorPart().divide(Math.sin(acos)).multiply(Math.sin(acos * s));
            );
        }

        // This is important
        public Quaternion multiply(Quaternion q) {
            final double s = (w * q.w) - getVectorPart().dot(q.getVectorPart());
            final Vector v = q.getVectorPart().multiply(w)
                    .add(getVectorPart().multiply(q.w))
                    .add(q.getVectorPart().cross(getVectorPart()));

            return new Quaternion(s, v);
        }

        // This negates only the vector part
        public Quaternion getConjugate() {
            return new Quaternion(w, -x, -y, -z);
        }
    }

Now let's go back and define the abstract methods in Vector.

    public class Vector {
        // Code omitted

        public Quaternion toPureQuaternion() {
            return new Quaternion(0, this);
        }

        // This will rotate the vector
        public Vector rotate(Quaternion rq) {
            return rq.multiply(toPureQuaternion()).multiply(rq.getConjugate()).getVectorPart();   
        }
    }

Now we can test it out!

    public final class Main {
        public static void main(String[] args) {
            final Vector v = new Vector(0, 0, 10);
            final Vector p = v.rotate(Quaternion.fromAxisAngle(Vector.POSTIIVE_Y, Math.toRadians(90)));

            // This will print (-10, 0, 0) with a small margin of error
            // If you want simulator-level precision, you can use BigDecimal instead of double
            System.out.println(p);

            // This should be quasi-zero if the operation succeeded
            final double magnitudeDiff = p.getMagnitude() - v.getMagnitude();
        }
    }

### Alright it works, but why?
I believe this [blog post](https://eater.net/quaternions) will do a much better job of explaining this.
All I did was convert that into usable Java code.
