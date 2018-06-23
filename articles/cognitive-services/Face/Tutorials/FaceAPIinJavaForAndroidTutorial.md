---
title: API Java szembesülhetnek Android oktatóanyag |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Hozzon létre egy egyszerű Android-alkalmazást, amely a kognitív szolgáltatások Arcfelismerési API észlelésére, és a kép emberi lapok kerete.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349410"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Ismerkedés az Arcfelismerési API-nak Java Android oktatóanyag

Ebből az oktatóanyagból megtudhatja, és hozzon létre egy egyszerű Android-alkalmazást, amely hívja meg a Arcfelismerési API emberi lapok észleléséhez a kép. Az alkalmazás az eredmény jeleníti meg, hogy az észlelt a lapok keretezési módot.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Előkészítése

Az oktatóanyag használatához szüksége lesz a következő előfeltételek teljesülését:

- Android Studio és az SDK telepítése
- Android-eszköz (tesztelési nem kötelező).

## <a name="step1"></a>1. lépés: Arcfelismerési API-szolgáltatásra, és az Előfizetés kulcs beszerzése

Arcfelismerési API-k használatához regisztrálnia kell a Microsoft kognitív Services portálon Arcfelismerési API előfizetni. Lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Elsődleges és másodlagos kulcs ebben az oktatóanyagban használható.

## <a name="step2"></a>2. lépés: Az alkalmazás-keretrendszer létrehozása

Ebben a lépésben létrehoz egy Android-alkalmazás projekt fel, és egy kép megjelenítése az alapszintű felhasználói felület megvalósításához. Egyszerűen csak kövesse az alábbi utasításokat: 

1. Nyissa meg az Android Studiót.
2. A Fájl menüben kattintson a **új projekt...**
3. Adjon nevet az alkalmazásnak **MyFirstApp**, majd kattintson a Tovább gombra. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Válassza ki a célplatform szükség szerint, és kattintson a Tovább gombra. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Válassza ki **alapvető tevékenység** és kattintson a Tovább gombra.
6. A tevékenység az alábbiak szerint nevet, és kattintson a Befejezés gombra. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Nyissa meg **activity_main.xml**, elrendezés szerkesztővel, ezzel a tevékenységgel kell megjelennie.
8. Forrás szövegfájl megtekintése, és módosítsa a tevékenység elrendezés az alábbiak szerint:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Nyissa meg **MainActivity.java** és a fájl elején a következő importálási irányelveket:

    ```java
    import java.io.*; 
    import android.app.*; 
    import android.content.*; 
    import android.net.*; 
    import android.os.*; 
    import android.view.*; 
    import android.graphics.*; 
    import android.widget.*; 
    import android.provider.*;
    ```
      
    Másodszor módosítsa az osztály az alábbiak szerint:  
    
    ```java
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;
         
    @Override
    protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           Button button1 = (Button)findViewById(R.id.button1);
           button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Az alkalmazás most már a gyűjteményből fénykép kereshetnek és megjeleníti azt az alábbi képen hasonló ablakban:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>3. lépés: Konfigurálja a Arcfelismerési API ügyféloldali kódtár

A Arcfelismerési API hívhat meg HTTPS használatával API-kérelmek felhő. Több kényelmesen a Arcfelismerési API használatával a .NET alkalmazások egy ügyfélkönyvtárat is biztosítja a foglalják magukban a webes kérelmek. Ebben a példában az ügyféloldali kódtár használjuk a munkahelyi leegyszerűsítése érdekében. 

Hajtsa végre az ügyféloldali kódtár konfigurálása az alábbi utasításokat: 

1. Keresse meg a legfelső szintű **build.gradle** fájlt a projekt a projekt panelről a példában látható módon. Vegye figyelembe, hogy nincsenek számos más **build.gradle** fájlok a projekt fa, és meg kell nyitnia a legfelső szintű **build.gradle** először fájlt.
2. Adja hozzá **mavenCentral()** a projektek adattárak számára. Jcenter(), amely az alapértelmezett tárház Android Studio, mivel jcenter() felülbírálja a mavenCentral() is használható.  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Nyissa meg a **build.gradle** fájlt a "app" projektben.
4. Az ügyféloldali kódtára a Maven központi tárházban tárolt függőségei hozzáadása:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Nyissa meg **MainActivity.java** a "app" projektben és a Beszúrás a következő importálási irányelveket: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Ezután helyezze be a következő kódot az osztályban:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Az első paraméter fent cserélje le az API-végpontot, amely a kulcs 1. lépésben lett hozzárendelve. Példa:
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   A második paraméter cserélje le az Előfizetés kulcs 1. lépésben beszerzett.
   
6. Nyissa meg a fájlt **AndroidManifest.xml** az "app" projektben. Helyezze be a következő elem gyermekeként a **manifest** elem:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Most már készen áll a Arcfelismerési API hívása az alkalmazásból. 

## <a name="step4"></a>4. lépés: A lapok észleléséhez képek feltöltése

A legegyszerűbb lapok észlelése módja meghívásával a [szembesülhetnek – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API közvetlenül a bináris fájl feltöltésével. Az ügyféloldali kódtár használata esetén ezt megteheti aszinkron módon **DetectAsync** , a **FaceServiceClient** osztály. Minden visszaadott lapot tartalmaz egy téglalapot elfoglalt helye választható arcfelismerési attribútumok sorozata együtt. Ebben a példában csak kell lekérni a tapasztalt helyét. Itt igazolnia kell a metódus beszúrása a **MainActivity** osztály arcfelismerési észleléséhez: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
                        return null;
                    }
                }
                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };
        detectTask.execute(inputStream);
    }
```

## <a name="step5"></a>5. lépés: Az be van jelölve a kép néz.

Ezen utolsó lépésében azt együtt kombinálhatja a fenti lépéseket, és jelölje a lemezképet a keretek észlelt lapokat. Első lépésként nyissa meg a **MainActivity.java** , majd szúrja be egy segédmetódust téglalapok megrajzolásához: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
        if (faces != null) {
            for (Face face : faces) {
                FaceRectangle faceRectangle = face.faceRectangle;
                canvas.drawRect(
                        faceRectangle.left,
                        faceRectangle.top,
                        faceRectangle.left + faceRectangle.width,
                        faceRectangle.top + faceRectangle.height,
                        paint);
            }
        }
        return bitmap;
    }
```

Most Befejezés a Teendőlista részt a **detectAndFrame** metódus keret lapokat, és jelentse állapotát.

```java
    @Override
    protected void onPreExecute() {
        detectionProgressDialog.show();
    }
    @Override
    protected void onProgressUpdate(String... progress) {
        detectionProgressDialog.setMessage(progress[0]);
    }
    @Override
    protected void onPostExecute(Face[] result) {
        detectionProgressDialog.dismiss();
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Végül adjon hozzá egy a **detectAndFrame** metódust a **onActivityResult** metódust, az alább látható módon. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Futtassa az alkalmazás és egy ARC tartalmazó kép keresse meg. Várjon, amíg a felhő API válaszolni néhány másodpercig. Ezt követően elérhetővé válik egy eredményt a hasonló az alábbi képen: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Összefoglalás

Ebben az oktatóanyagban megtanulta, a folyamat alapvetően a Arcfelismerési API használatával, és az alkalmazások arcfelismerési jelek megjelenő képek. A Arcfelismerési API-val további információkért tekintse meg az útmutató és [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Kapcsolódó oktatóprogramok

- [A csharp nyelvű oktatóprogram Ismerkedés a Arcfelismerési API](FaceAPIinCSharpTutorial.md)
- [Az oktatóanyag a Python Ismerkedés a Arcfelismerési API](FaceAPIinPythonTutorial.md)
