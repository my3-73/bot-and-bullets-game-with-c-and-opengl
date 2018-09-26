# bot-and-bullets-game-with-c-and-opengl



#include<iostream>
#include<GL/gl.h>
#include<GL/glut.h>
#include<stdlib.h>
#include<stdio.h>
#include<windows.h>

int i,q;
int score = 0;
int highscore = 0;
int screen = 0;
bool collide = false;
bool bulletshoot = false;
bool bulletHit = false;
char buffer[10];

int spaceshipX = 200, spaceshipY = 70;
int hurdleX[4], hurdleY[4];
int divx = 250, divy = 4, movd;
int shootX,shootY;

void drawText(char ch[],int xpos, int ypos)//draw score/message on screen
{
    int numofchar = strlen(ch);
    glLoadIdentity ();
    glRasterPos2f( xpos, ypos);
    for (i = 0; i < numofchar; i++)
    {
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, ch[i]);
    }
}


void drawTextNum(char ch[],int numtext,int xpos, int ypos)//counting the score
{
    int len;
    int k;
    k = 0;
    len = numtext - strlen (ch);
    glLoadIdentity ();
    glRasterPos2f( xpos, ypos);
    for (i = 0; i <=numtext - 1; i++)
    {
        if ( i < len )
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18,'0');
        else
        {
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, ch[k]);
            k++;
        }

    }
}


void hurdlePos()
{
    glClearColor(0,0,1,0);
    for(i = 0; i < 4; i++)
    {
        if(rand() % 2 == 0)
        {
            hurdleX[i] = 200;
        }
        else
        {
            hurdleX[i] = 300;
        }
        hurdleY[i] = 1000 - i * 200;
    }
}


void drawSpace()
{
    glBegin(GL_QUADS);
    glColor3f(0,0,0);//000
    glVertex2f(50, 500);
    glVertex2f(50, 0);
    glVertex2f(450, 0);
    glVertex2f(450, 500);
    glEnd();
}

void drawSpaceship()
{
    glPointSize(10.0);

    glBegin(GL_POINTS);//front shooter
    glColor3f(1,1,1);//111
    glVertex2f(spaceshipX, spaceshipY +40);
    glEnd();

    glBegin(GL_QUADS);
    glColor3f(0.137255,0.419608,0.556863);//middle body
    glVertex2f(spaceshipX - 25, spaceshipY + 25);
    glVertex2f(spaceshipX - 25, spaceshipY - 25);
    glVertex2f(spaceshipX + 25, spaceshipY - 25);
    glVertex2f(spaceshipX + 25, spaceshipY + 25);
    glEnd();

    glBegin(GL_POINTS);//design on middle
    glColor3f(1,0,0);
    glVertex2f(spaceshipX-10,spaceshipY-5);
    glVertex2f(spaceshipX+10,spaceshipY-5);
    glEnd();

    glBegin(GL_POINTS);//thrusters
    glColor3f(1,1,1);
    glVertex2f(spaceshipX-10,spaceshipY-30);
    glVertex2f(spaceshipX+10,spaceshipY-30);
    glEnd();

    glBegin(GL_TRIANGLES);//right wing
    glColor3f(0.137255,0.419608,0.556863);
    glVertex2f(spaceshipX+25,spaceshipY+25);
    glVertex2f(spaceshipX+25,spaceshipY-25);
    glVertex2f(spaceshipX+45,spaceshipY-35);
    glEnd();

    glBegin(GL_TRIANGLES);//left wing
    glColor3f(0.137255,0.419608,0.556863);
    glVertex2f(spaceshipX-25,spaceshipY+25);
    glVertex2f(spaceshipX-25,spaceshipY-25);
    glVertex2f(spaceshipX-45,spaceshipY-35);
    glEnd();


    glBegin(GL_QUADS);//up body
    glColor3f(0.99609, 0.83984, 0);
    glVertex2f(spaceshipX - 25, spaceshipY + 25);
    glVertex2f(spaceshipX - 18, spaceshipY + 40);
    glVertex2f(spaceshipX + 18, spaceshipY + 40);
    glVertex2f(spaceshipX + 25, spaceshipY + 25);
    glEnd();

}

void drawHurdle()//hurdles
{

    for(i = 0; i < 4; i++)
    {
        // hurdle
        glBegin(GL_TRIANGLES);
        glColor3f(0.89,0.47,0.20);
        glVertex2f(hurdleX[i], hurdleY[i] + 20);
        glVertex2f(hurdleX[i] - 23, hurdleY[i] - 35);
        glVertex2f(hurdleX[i] + 23, hurdleY[i] - 35);
        glEnd();

        hurdleY[i] = hurdleY[i] - 5;

        if(hurdleY[i] > spaceshipY - 23 - 23 && hurdleY[i] < spaceshipY + 35 + 35 && hurdleX[i] == spaceshipX)
        {
            collide = true;
        }

        if(hurdleY[i] - shootY < 20 && hurdleX[i] == shootX)
        {
            bulletHit = true;
            hurdleY[i] = -100;
            hurdleX[i] = -100;
            shootY = -100;
            shootX = -100;

        }

        if(hurdleY[i] < -25)
        {
            if(rand() % 2 == 0)
            {
                hurdleX[i] = 200;
            }
            else
            {
                hurdleX[i] = 300;
            }
            hurdleY[i] = 800;
        }
    }
}

void shootHurdle()
{
    if(bulletHit == false)
    {
        glBegin(GL_POINTS);//front shooter
        glColor3f(255,255,255);
        glVertex2f(shootX, shootY);
        glVertex2f(shootX, shootY+50);
        shootY += 5;
        glEnd();
    }
    else
    {
        bulletshoot = false;
        bulletHit = false;
    }
}

void keyboard(unsigned char key, int x, int y)
{
    switch(key)
    {
    case ' ':
        shootX = spaceshipX;
        shootY = spaceshipY+50;
        bulletshoot = true;
        break;
    case 'q':
        exit(EXIT_SUCCESS);
    }
}

void Specialkey(int key, int x, int y)
{
    switch(key)
    {
    case GLUT_KEY_UP:
        if(collide == false)
        {
            for(i = 0; i <4; i++)
            {
                hurdleY[i] = hurdleY[i] - 10;
            }
            movd = movd - 20;
        }
        break;
    case GLUT_KEY_DOWN:
        if(collide == false)
        {
            for(i = 0; i <4; i++)
            {
                hurdleY[i] = hurdleY[i] + 13;
            }
            movd = movd + 5;
        }
        break;
    case GLUT_KEY_LEFT:
        spaceshipX = 200;
        break;
    case GLUT_KEY_RIGHT:
        spaceshipX = 300;
        break;
    case GLUT_KEY_HOME:
        collide = false;
        score = 0;
        if(spaceshipX == 300)
        {
            spaceshipX = 200;
        }
        else if(spaceshipX == 200)
        {
            spaceshipX = 300;
        }
        break;
    }

    glutPostRedisplay();
}

void init()
{
	glClearColor(1,1,0,0);
    //glClearColor(0.62352,0.372549,0.623529,0);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, 500, 0, 500);
    glMatrixMode(GL_MODELVIEW);
}


void display()
{
    if(collide != true)
    {
        glClear(GL_COLOR_BUFFER_BIT);
        drawSpace();
        drawSpaceship();
        drawHurdle();
        movd = movd - 16;
        if(movd < - 60)
        {
            movd = 0;
        }
        if(bulletshoot == true)
        {
            shootHurdle();
        }

        score = score + 1;

        glColor3f(1,1,1);
        drawText("Score:",36,455);
        itoa (score, buffer, 10);
        drawTextNum(buffer, 6, 420,455);

        drawText("High Score:",10,455);
        if(score>highscore)
        {
            highscore = score;
            itoa(highscore,buffer,10);
            drawTextNum(buffer,6,100,455);
        }
        else
        {
            itoa(highscore,buffer,10);
            drawTextNum(buffer,6,100,455);
        }

        glutSwapBuffers();
        for(q = 0; q<= 10000000; q++)
        {
            ;
        }
    }
    else
    {
        glColor3f(1,1,1);
        drawText("Game Over, Click Home to Restart or click q to quit the game", 200,250);
        glutSwapBuffers();
    }
}


int main(int argc, char **argv)
{
    glutInit(&argc,argv);
    glutInitDisplayMode(GLUT_RGB|GLUT_DOUBLE);
    glutInitWindowPosition(100,100);
    glutInitWindowSize(1000,500);
    glutCreateWindow("BOT AND BULLETS GAME");
    hurdlePos();
    init();
    glutDisplayFunc(display);
    glutSpecialFunc(Specialkey);
    glutKeyboardFunc(keyboard);
    glutIdleFunc(display);
    glutMainLoop();
    return 0;
}
