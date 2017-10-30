A small patch for POVRay-3.7 to give the depth-map of the rendered image as well as camera parameters (external as well as internal). All we need to modify is the code in 

*DEPTH MAP*

For depth map files, the following changes have been incorporated:

(a) Class view (defined in $POVRAYDIR/source/backend/scene/view.[h/cpp]) where a double array that stores the depth values of the pixels, is added. 

(b) Modify the code in class 
    Tracetask 
    as well as 
    Tracepixel 
    (defined in $POVRAYDIR/source/backend/render/tracetask|tracepixel.[h/cpp]) where
     1. Function  TraceTask::Run() calls SimpleSamplingM0()
     2. In SimpleSampleM0(), the function call 
	trace(x, y, GetViewData()->GetWidth(), GetViewData()->GetHeight(), col) has been modified to 
	trace(x, y, GetViewData()->GetWidth(), GetViewData()->GetHeight(), col, depthVal).
     3. The trace() function calls the TracePixel::operator(..) function which saves the depthvalue.

(c) view.cpp has destructor where we write the depth file.

(d) Once we get the deptharray with depthvalues stored after going through succession of this series of function calls, we dump the values into a file with the extension .depth 
    but same base name as the     name of the outputfile. As an example if you typed: 
    
    povray +Imyscene.pov +Omyoutput.png +W640 +H480 -d 
    
Apart from myoutput.png you'd get myoutput.depth file which contains the depthvalues of each pixel. The file stores the depth values in a simple linear array where 
entries are stored in row-major format.If you were using matlab to read the depth files, you could just simply use the commands (given below) to store the depthvalues 
in a 2-D (image array style) array. 

   1. depthvalues = load('myoutput.depth');
   2. deptharray  = reshape(depthvalues,640,480)';

(e) Filename is extracted from view.cpp's StartRender(..) function which reads the POVMS_Object& renderOptions Object and it is here where the name of the output file is stored 
    and we stole the basename from here and then converted with UCS2toASCIIString() to obtain the C++ String.


*CAMERA PARAMETERS*

Camera Parameters can be obtained from into the backend directory in $POVRAYDIR/source directory and found out in the scene data.

(a) class Scene (defined in $POVRAYDIR/source/backend/scene/scene.[h/cpp]) has a public member a pointer to SceneData (named as sceneData and is a shared pointer).

(b) This SceneData class has an object of Camera (defined in $POVRAYDIR/source/backend/scene/camera.[h/cpp]) hiding under the name of parsedCamera.

(c) All you need to do is to just write down all the public variables of parsedCamera (Location, LookAt, Up, Right, Sky etc.) into the output file (.txt).

So in summary if you typed: 

    povray +Imyscene.pov +Omyoutput.png +W640 +H480 -d
    
you would obtain myoutput.txt as well as myoutput.depth with myoutput.png



Ankur Handa
Contact: <handa.ankur@gmail.com> 
