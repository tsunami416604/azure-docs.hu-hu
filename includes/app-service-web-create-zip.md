## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

Győződjön meg arról, hogy még a mintaprojekt gyökérkönyvtárában van. Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Később ezt a ZIP-fájlt fogja feltölteni az Azure-ba, majd üzembe helyezni az App Service-ben.