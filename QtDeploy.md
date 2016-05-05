# How to deploy Qt Windows application

Using windeployqt.exe is an easiest way to deploy Qt Windows application.

1. Make deployment directory, e.g. deploy
2. cd deploy
3. windeployqt --release --qmldir <qml-dir-location> <exe-location>  
e.g. windeployqt --release --qmldir C:\Work\QTestRunner\ui QTestRunner.exe

You may install Visual Studio C++ redistribution if your application has been compiled by using Visual Studio C++.


