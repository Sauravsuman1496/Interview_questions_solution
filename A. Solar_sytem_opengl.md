## Code

```cpp
#include <GL/glut.h>

float angleEarth = 0;   // Earth revolution angle
float rotEarth = 0;     // Earth rotation angle
float angleMoon = 0;    // Moon revolution angle

void init()
{
    glEnable(GL_DEPTH_TEST);
    // depth test on

    glEnable(GL_LIGHTING);
    glEnable(GL_LIGHT0);
    // enable lighting

    glEnable(GL_COLOR_MATERIAL);
    glColorMaterial(GL_FRONT, GL_AMBIENT_AND_DIFFUSE);
    // color material for lighting

    GLfloat lightPos[] = {0.0f, 0.0f, 0.0f, 1.0f};
    glLightfv(GL_LIGHT0, GL_POSITION, lightPos);
    // light at sun position

    glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
    // black background
}

void display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glLoadIdentity();

    gluLookAt(
        0,10,20,
        0,0,0,
        0,1,0);
    // camera setup

    // Draw Sun
    glColor3f(1,1,0);
    // yellow color
    glutSolidSphere(2,50,50);

    // Draw Earth
    glPushMatrix();
    // save matrix

        glRotatef(angleEarth,0,1,0);
        // Earth revolves around sun

        glTranslatef(8,0,0);
        // distance from sun

        glRotatef(rotEarth,0,1,0);
        // Earth self rotation

        glColor3f(0,0,1);
        // blue color

        glutSolidSphere(1,30,30);
        // draw Earth

        // Draw Moon
        glPushMatrix();
        // save Earth matrix

            glRotatef(angleMoon,0,1,0);
            // Moon revolves around earth

            glTranslatef(2,0,0);
            // distance from earth

            glColor3f(0.8,0.8,0.8);
            // grey color

            glutSolidSphere(0.3,20,20);
            // draw Moon

        glPopMatrix();
        // restore Earth matrix

    glPopMatrix();
    // restore original matrix

    glutSwapBuffers();
}

void update(int value)
{
    angleEarth += 0.3;
    rotEarth += 2.0;
    angleMoon += 1.5;
    // increase angles

    if(angleEarth > 360) angleEarth = 0;
    if(rotEarth > 360) rotEarth = 0;
    if(angleMoon > 360) angleMoon = 0;
    // reset after 360

    glutPostRedisplay();

    glutTimerFunc(16, update, 0);
    // call again for animation
}

void reshape(int w,int h)
{
    glViewport(0,0,w,h);

    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();

    gluPerspective(45,(float)w/h,1,100);
    // perspective projection

    glMatrixMode(GL_MODELVIEW);
}

int main(int argc,char** argv)
{
    glutInit(&argc,argv);

    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH);
    glutInitWindowSize(800,600);
    glutCreateWindow("3D Solar System");
    // create window

    init();

    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutTimerFunc(16,update,0);
    // register callbacks

    glutMainLoop();

    return 0;
}
```

## Steps and Concepts Used

**1. Setting up the environment**
- Use OpenGL with GLUT to create a window, handle rendering, and manage the animation loop.
- Initialize display mode with double buffering (`GLUT_DOUBLE`) for smooth animation, RGB color mode, and a depth buffer (`GLUT_DEPTH`) for correct 3D rendering.

**2. Enabling Depth Testing**
- Enable `GL_DEPTH_TEST` so objects closer to the camera correctly occlude objects farther away.

**3. Setting up Lighting**
- Enable `GL_LIGHTING` and `GL_LIGHT0` to simulate the Sun as a light source.
- Position the light at the origin (0,0,0), same as the Sun's position, so it illuminates Earth and Moon realistically.
- Use `GL_COLOR_MATERIAL` so object colors set via `glColor3f()` respond correctly to lighting (ambient and diffuse components).

**4. Hierarchical Modeling (Scene Graph Concept)**
- Represent the solar system as a hierarchy: Sun → Earth → Moon.
- Use `glPushMatrix()` / `glPopMatrix()` to isolate transformations for each object:
  - Moon's transformations are relative to Earth's position (child of Earth).
  - Earth's transformations are relative to the Sun's position (child of Sun).
- This ensures that when Earth moves, the Moon automatically moves with it, like a real parent-child hierarchy.

**5. Applying Transformations**
- **Rotation (`glRotatef`)**: simulates orbital revolution and axial spin.
- **Translation (`glTranslatef`)**: positions Earth at a fixed distance from Sun, and Moon at a fixed distance from Earth.
- Order matters: rotate first (orbit around parent), then translate (move outward), then rotate again for self-spin.

**6. Drawing the Celestial Bodies**
- Use `glutSolidSphere(radius, slices, stacks)` for Sun, Earth, and Moon with different sizes.
- Different colors: yellow (Sun), blue (Earth), gray (Moon).

**7. Camera Setup**
- Use `gluLookAt()` to position the camera so the whole system is visible.
- Use `gluPerspective()` in the reshape function for perspective projection.

**8. Animation Loop**
- Use `glutTimerFunc()` to repeatedly call an update function (~60 FPS).
- Increment orbit/spin angles, reset after 360 degrees.
- Call `glutPostRedisplay()` to trigger re-rendering.

**9. Handling Window Resize**
- Reshape callback updates viewport and projection matrix to maintain aspect ratio.

**10. Rendering Loop**
- `glutDisplayFunc()` registers the draw function.
- `glutMainLoop()` starts the event-driven loop that keeps calling display/update until exit.
