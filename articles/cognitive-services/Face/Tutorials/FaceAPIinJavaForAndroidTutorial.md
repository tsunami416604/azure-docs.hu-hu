---
title: 'Oktatóanyag: Arcok felismerése és bekeretezése egy képen az Android SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy egyszerű Android-alkalmazást hozunk létre, amely a Face API segítségével észleli és bekeretezi a képeken lévő arcokat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: pafarley
ms.openlocfilehash: 99b2734745df722f45443b5347ae6dd054c8aa31
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957037"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Oktatóanyag: Android-alkalmazás készítése képeken lévő arcok észleléséhez és bekeretezéséhez

Ebben az oktatóanyagban egy egyszerű Android-alkalmazást fogunk létrehozni, amely az arcfelismerési szolgáltatás Java-osztálytárának segítségével észleli a képeken lévő emberi arcokat. Az alkalmazás téglalappal bekeretezve jeleníti meg az összes észlelt arcot a kiválasztott képen. A teljes mintakód elérhető a GitHubon a [képeken lévő arcok észlelését és bekeretezését az Androidon](https://github.com/Azure-Samples/cognitive-services-face-android-sample) ismertető témakörben.

![Android-képernyőkép egy fényképről, amelyen az arcok piros téglalappal vannak bekeretezve](../Images/android_getstarted2.1.PNG)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Android-alkalmazás létrehozása
> - Az arcfelismerési szolgáltatás ügyfélkódtárának telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré

## <a name="prerequisites"></a>Előfeltételek

- A minta futtatásához előfizetési kulcs szükséges. Ingyenes próba előfizetési kulcsot itt szerezhet: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) legalább 22-es SDK-val (ez az arcfelismerési ügyfélkódtár számára szükséges).
- A [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) arcfelismerési ügyfélkódtár a Mavenből. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="create-the-project"></a>A projekt létrehozása

Az Android-alkalmazásprojekt létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg az Android Studiót. Ez az oktatóanyag az Android Studio 3.1-es verzióját használja.
1. Válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.
1. A **Create Android Project** (Android-projekt létrehozása) képernyőn szükség esetén módosítsa az alapértelmezett mezőket, majd kattintson a **Next** (Tovább) gombra.
1. A **Target Android Devices** (Cél Android-eszközök) képernyőn a legördülő listából válassza az **API 22** vagy magasabb értéket, majd kattintson a **Next** (Tovább) gombra.
1. Válassza az **Empty Activity** (Üres tevékenység) elemet, majd kattintson a **Next** (Tovább) gombra.
1. Törölje a **Backwards Compatibility** (Visszamenőleges kompatibilitás) beállítás bejelölését, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>A kép kiválasztására és megjelenítésére szolgáló felhasználói felület létrehozása

Nyissa meg az *activity_main.xml* fájlt. Ekkor megjelenik a Layout Editor (Elrendezésszerkesztő). Válassza a **Text** (Szöveg) fület, majd cserélje le a tartalmat a következő kódra.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Nyissa meg a *MainActivity.java* elemet, majd cseréljen le mindent a következő kódra az első `package` utasítást kivéve.

A kód beállít egy eseménykezelőt a `Button` elemre, amely elindít egy új tevékenységet. Ez lehetővé teszi a felhasználó számára, hogy kiválasszon egy képet. A kiválasztott kép megjelenik az `ImageView` elemben.

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

public class MainActivity extends Activity {
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
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Ekkor az alkalmazás keres és megjelenít egy fényképet az ablakban az alábbi képen láthatóhoz hasonlóan.

![Android-képernyőkép egy fényképről, amelyen arcok láthatóak](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Az arcfelismerési ügyfélkódtár konfigurálása

A Face API egy felhőalapú API, amely HTTPS-kérések használatával hívható meg. Ez az oktatóanyag az arcfelismerési ügyfélkódtárat használja, amely magában foglalja ezeket a webes kéréseket, hogy megkönnyítse az Ön munkáját.

A **Project** (Projekt) panelen válassza az **Android** elemet a legördülő listából. Bontsa ki a **Gradle Scripts** (Gradle-szkriptek) elemet, majd nyissa meg a *build.gradle (Module: app)* elemet.

Adjon hozzá egy függőséget a `com.microsoft.projectoxford:face:1.4.3` arcfelismerési ügyfélkódtárhoz, ahogyan az az alábbi képernyőképen látható, majd kattintson a **Sync Now** (Szinkronizálás most) gombra.

![Android Studio-képernyőkép az alkalmazás build.gradle fájljáról](../Images/face-tut-java-gradle.png)

Nyissa meg a **MainActivity.java** fájlt, és fűzze hozzá a következő importálási irányelveket:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Az arcfelismerési ügyfélkódtár kódjának hozzáadása

Illessze be az alábbi kódot az `onCreate` metódus fölötti `MainActivity` osztályba:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Az `<API endpoint>` helyett írja be a kulcshoz rendelt API-végpontot. Az ingyenes próba előfizetési kulcsok létrehozásának helye a **westcentralus** régió. Tehát ha ingyenes próba előfizetési kulcsot használ, az utasítás legyen a következő:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

A `<Subscription Key>` helyére írja be az előfizetési kulcsot. Például:

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

A **Project** (Projekt) panelen bontsa ki az **app** elemet, majd a **manifests** elemet, és nyissa meg az *AndroidManifest.xml* fájlt.

Illessze be a következő elemet a `manifest` elem közvetlen gyermekeként:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Hozza létre a projektet a hibák ellenőrzéséhez. Most már készen áll arra, hogy meghívja az arcfelismerési szolgáltatást.

## <a name="upload-an-image-to-detect-faces"></a>Képek feltöltése arcok észleléséhez

Az arcok észlelésének legegyszerűbb módja a `FaceServiceClient.detect` metódus meghívása. Ez a metódus becsomagolja az [észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-metódust, és `Face` elemek tömbjét adja vissza.

Minden visszaadott `Face` elem egy téglalapot foglal magában, amely jelzi annak helyét és esetleg további választható arcattribútumokat. Ebben a példában csak az arcok helye szükséges.

Hiba esetén egy megjelenő `AlertDialog` jelzi annak okát.

Illessze be a következő metódusokat a `MainActivity` osztályba.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
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

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>A képeken lévő arcok bekeretezése

Illessze be a következő segédmetódust a `MainActivity` osztályba. Ez a módszer téglalapot rajzol minden észlelt arc köré.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
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

Végezze el az `AsyncTask` metódusokat, amelyeket `TODO` megjegyzések jelölnek a `detectAndFrame` metódusban. Sikeres művelet esetén a kiválasztott kép megjelenik a bekeretezett arcokkal az `ImageView` elemben.

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
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Végül az `onActivityResult` metódusban távolítsa el a megjegyzés jelölést a `detectAndFrame` metódus hívása mellől.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. Ezután az eredmény az alábbi képhez hasonló lesz:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Összegzés

Ebben az oktatóanyagban megismerte az arcfelismerési szolgáltatás használatához szükséges alapvető folyamatot, és létrehozott egy alkalmazást, amely bekeretezett arcokat jelenít meg a képeken.

## <a name="next-steps"></a>További lépések

További tudnivalók arcok részeinek észleléséről és használatáról.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Felfedezheti a Face API-kat, amelyek arcok és azok attribútumai, például testtartás, nem, kor, fejtartás, arcszőrzet és szemüveg észleléséhez használhatóak.

> [!div class="nextstepaction"]
> [Face API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).