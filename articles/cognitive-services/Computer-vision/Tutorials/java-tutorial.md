---
title: 'Oktatóanyag: Computer Vision API Java'
titlesuffix: Azure Cognitive Services
description: Ismerkedés egy alapszintű Java Swing-alkalmazással, amely a Computer Vision API-t használja az Azure Cognitive Services szolgáltatásban. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.date: 09/21/2017
ms.openlocfilehash: cca35d031e860e014c8fd84b0daf6b4d60d18046
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985847"
---
# <a name="tutorial-computer-vision-api-java"></a>Oktatóanyag: Computer Vision API Java

Ez az oktatóanyag az Azure Cognitive Services Computer Vision REST API funkcióit mutatja be.

Ismerjen meg egy Java Swing-alkalmazást, amely a Computer Vision REST API segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. Ebben a példában megadhatja egy kép URL-címét elemzés vagy feldolgozás céljából. Ez a nyílt forráskódú példa sablonként használható saját, Computer Vision REST API használatához készült Java-alkalmazások fejlesztéséhez.

Az oktatóanyag a Computer Vision következő használati területeit ismerteti:

> [!div class="checklist"]
> * Rendszerkép elemzése
> * Természetes vagy mesterséges nevezetességek azonosítása képeken
> * Hírességek azonosítása képeken
> * Jó minőségű miniatűrök készítése képekből
> * Képeken látható nyomtatott szöveg felolvasása
> * Képeken látható, kézzel írott szöveg felolvasása

A Java Swing-űrlapalkalmazást már létrehoztuk, azonban még működésképtelen. Ebben az oktatóanyagban a Computer Vision REST API kódját hozzáadva látja el funkciókkal az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Ezt az oktatóanyagot a NetBeans IDE használatával hoztuk létre. Egészen pontosan a NetBeans **Java SE** verziójával, amelyet [innen tölthet le](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Előfizetés a Computer Vision API-ra és egy előfizetési kulcs beszerzése 

A példa létrehozása előtt elő kell fizetnie a Computer Vision API-ra, amely az Azure Cognitive Services része. Az előfizetésről és a kulcskezelésről további információt az [Előfizetések](https://azure.microsoft.com/try/cognitive-services/) című témakörben találhat. Ebben az oktatóanyagban az elsődleges és másodlagos kulcsok is használhatók. 

## <a name="download-the-tutorial-project"></a>Az oktatóprojekt letöltése

1. Lépjen a [Cognitive Services Java Computer Vision – oktatóanyag](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial) tárházra.
1. Kattintson a **Klónozás vagy letöltés** lehetőségre.
1. Kattintson a **ZIP letöltése** elemre az oktatóprojekt .zip fájljának letöltéséhez.

A .zip fájlt nem kell kicsomagolni, mivel a NetBeans ebből importálja a projektet.

## <a name="import-the-tutorial-project"></a>Az oktatóprojekt importálása

Importálja a **cognitive-services-java-computer-vision-tutorial-master.zip** fájlt a NetBeansbe.

1. A NetBeansben kattintson a **Fájl** > **Projekt importálása** > **ZIP-fájlból...** lehetőségre. Megjelenik a **Projektek importálása ZIP-fájlból** párbeszédpanel.
1. A **ZIP-fájl:** mezőben a **Tallózás** gombra kattintva keresse meg a **cognitive-services-java-computer-vision-tutorial-master.zip** fájlt, majd kattintson a **Megnyitás** gombra.
1. Kattintson a **Projektek importálása ZIP-fájlból** párbeszédpanel **Importálás** elemére.
1. A **Projektek** panelen bontsa ki a **ComputerVision** > **Forráscsomagok** > **&lt;Alapértelmezett csomag&gt;** elemet. 
   A NetBeans egyes verzióiban **src** elem jelenhet meg a **Forráscsomagok** > **&lt;Alapértelmezett csomag&gt;** helyett. Ebben az esetben bontsa ki az **src** területet.
1. Kattintson duplán a **MainFrame.java** elemre a fájl a NetBeans szerkesztőben való megnyitásához. Megjelenik a **MainFrame.java** fájl **Tervezés** lapja.
1. A Java forráskód megtekintéséhez kattintson a **Forrás** lapra.

## <a name="build-and-run-the-tutorial-project"></a>Az oktatóprojekt létrehozása és futtatása

1. Nyomja le az **F6** billentyűt az oktatóalkalmazás létrehozásához és futtatásához.

    Az oktatóalkalmazásban kattintson egy lapra az adott funkció paneljének megjelenítéséhez. A gombokhoz üres metódusok tartoznak, így azok nem csinálnak semmit.

    Az ablak alján az **Előfizetési kulcs** és az **Előfizetési régió** mező található. Ezekben egy érvényes előfizetési kulcsot, illetve az ahhoz tartozó régiót kell megadnia. Előfizetési kulcs beszerzéséhez tekintse meg az [Előfizetések](https://azure.microsoft.com/try/cognitive-services/) című témakört. Ha megkapta a hivatkozásból elérhető ingyenes próbaverziós előfizetési kulcsot, az alapértelmezett **westcentralus** az előfizetési kulcsok megfelelő régiója.

1. Lépjen ki az oktatóalkalmazásból.

## <a name="add-the-tutorial-code"></a>Oktatókód hozzáadása

A Java Swing-alkalmazásban hat lap található. Mindegyik lap egy Computer Vision-funkciót ismertet (elemzés, OCR és hasonlók). A hat oktatószakasz nem függ egymástól, így hozzáadhat egyet, többet, vagy akár mind a hatot is. Ezt bármilyen sorrendben megteheti.

Lássunk hozzá.

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A Computer Vision Elemzés funkciója több mint 2000 felismerhető tárgy, élőlény, táj és cselekvés alapján elemzi a képeket. Az elemzés után az Elemzés egy JSON-objektumot hoz létre, amely leíró címkékkel, színelemzéssel, feliratokkal és egyebekkel ismerteti a képet.

Az oktatóalkalmazás Elemzés funkciójának végrehajtásához tegye a következőket:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Elemzés – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

Az **analyzeImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **AnalyzeImage** metódust a kép elemzéséhez. Amikor az **AnalyzeImage** elkészül, a metódus megjeleníti a formázott JSON-választ a **Válasz** szövegterületen, kinyeri az első feliratot a **JSONObject** fájlból, és megjeleníti a feliratot, valamint az annak helyességére vonatkozó konfidenciaszintet.

Illessze be az alábbi kódot az **analyzeImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Elemzés – 2. lépés: A REST API-hívás burkolójának hozzáadása

Az **AnalyzeImage** metódus burkolja a REST API-hívást a kép elemzéséhez. A metódus visszaad egy **JSONObject** elemet, amely ismerteti a képet, vagy **null** értéket eredményez, ha hiba történt.

Illessze be az **AnalyzeImage** metódust az **analyzeImageButtonActionPerformed** alá.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

### <a name="analyze-step-3-run-the-application"></a>Elemezés – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Adja meg egy elemzendő kép URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="recognize-a-landmark"></a>Nevezetesség felismerése

A Computer Vision Nevezetesség funkciója természetes és mesterséges nevezetességeket (például hegyeket vagy híres épületeket) elemez a képeken. Az elemzés befejezése után a Nevezetesség egy JSON-objektumot eredményez, amely a képen található nevezetességeket azonosítja.

Az oktatóalkalmazás Nevezetesség funkciójának végrehajtásához tegye a következőket:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Nevezetesség – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

A **landmarkImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **LandmarkImage** metódust a kép elemzéséhez. Amikor a **LandmarkImage** elkészül, a metódus megjeleníti a formázott JSON-választ a **Válasz** szövegterületen, kinyeri az első nevezetességnevet a **JSONObject** fájlból, és megjeleníti az ablakon a helyes azonosításra vonatkozó konfidenciaszinttel együtt.

Illessze be az alábbi kódot a **landmarkImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Nevezetesség – 2. lépés: A REST API-hívás burkolójának hozzáadása

A **LandmarkImage** metódus burkolja a REST API-hívást a kép elemzéséhez. A metódus visszaad egy **JSONObject** elemet, amely ismerteti a képen található nevezetességeket, vagy **null** értéket eredményez, ha hiba történt.

Illessze be a **LandmarkImage** metódust a **landmarkImageButtonActionPerformed** metódus alá.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="landmark-step-3-run-the-application"></a>Nevezetesség – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Kattintson a **Nevezetesség** lapra, adja meg egy elemzendő nevezetesség URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="recognize-celebrities"></a>Hírességek felismerése

A Computer Vision Hírességek funkciója hírességek alapján elemez egy képet. Az elemzés befejezése után a Hírességek egy JSON-objektumot eredményez, amely a képen található hírességeket azonosítja.

Az oktatóalkalmazás Hírességek funkciójának végrehajtásához tegye a következőket:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Hírességek – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

A **celebritiesImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **CelebritiesImage** metódust a kép elemzéséhez. Amikor a **CelebritiesImage** elkészül, a metódus megjeleníti a formázott JSON-választ a **Válasz** szövegterületen, kinyeri az első hírességet a **JSONObject** fájlból, és megjeleníti az ablakon a helyes azonosításra vonatkozó konfidenciaszinttel együtt.

Illessze be az alábbi kódot a **celebritiesImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Hírességek – 2. lépés: A REST API-hívás burkolójának hozzáadása

A **CelebritiesImage** metódus burkolja a REST API-hívást a kép elemzéséhez. A metódus visszaad egy **JSONObject** elemet, amely ismerteti a képen található hírességeket, vagy **null** értéket eredményez, ha hiba történt.

Illessze be a **CelebritiesImage** metódust a **celebritiesImageButtonActionPerformed** metódus alá.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="celebrities-step-3-run-the-application"></a>Hírességek – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Kattintson a **Hírességek** lapra, adja meg egy elemzendő híresség URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="intelligently-generate-a-thumbnail"></a>Miniatűr intelligens létrehozása

A Computer Vision Miniatűr funkciója miniatűrt készít egy képből. Az **intelligens körbevágás** funkció segítségével a Miniatűr funkció azonosítja egy kép lényeges területét, majd ez alapján egy miniatűrt hoz létre, így mindig esztétikusabb miniatűröket készít.

Az oktatóalkalmazás Miniatűr funkciójának végrehajtásához tegye a következőket:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Miniatűr – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

A **thumbnailImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **getThumbnailImage** metódust a miniatűr létrehozásához. Amikor a **getThumbnailImage** lefut, a metódus megjeleníti a létrehozott miniatűrt.

Illessze be az alábbi kódot a **thumbnailImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Miniatűr – 2. lépés: A REST API-hívás burkolójának hozzáadása

A **getThumbnailImage** metódus burkolja a REST API-hívást a kép elemzéséhez. A metódus egy **BufferedImage** elemet eredményez, amely tartalmazza a miniatűrt, vagy **null** értéket ad, ha hiba történt. A hibaüzenet a **jsonError** sztring első elemében jelenik meg.

Illessze be a **getThumbnailImage** metódust a **thumbnailImageButtonActionPerformed** metódus alá.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

### <a name="thumbnail-step-3-run-the-application"></a>Miniatűr – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Kattintson a **Miniatűr** lapra, adja meg egy kép URL-címét, majd kattintson a **Miniatűr létrehozása** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="read-printed-text-ocr"></a>Nyomtatott szöveg felolvasása (OCR)

A Computer Vision optikai karakterfelismerés (OCR) funkciója nyomtatott szöveget tartalmazó képeket elemez. Az elemzés befejezése után az OCR egy JSON-objektumot hoz létre, amely tartalmazza a szöveget, valamint annak a képen elfoglalt helyét.

Az oktatóalkalmazás OCR funkciójának végrehajtásához tegye a következőket:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

Az **ocrImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **OcrImage** metódust a kép elemzéséhez. Amikor az **OcrImage** lefut, a metódus formázott JSON-ként jeleníti meg az észlelt szöveget a **Válasz** szövegterületen.

Illessze be az alábbi kódot az **ocrImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR – 2. lépés: A REST API-hívás burkolójának hozzáadása

Az **OcrImage** metódus burkolja a REST API-hívást a kép elemzéséhez. A metódus visszaad egy, a hívásból származó JSON-adatokon alapuló **JSONObject** elemet, vagy **null** értéket eredményez, ha hiba történt.

Illessze be az **OcrImage** metódust az **ocrImageButtonActionPerformed** alá.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="ocr-step-3-run-the-application"></a>OCR – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Kattintson az **OCR** lapra, adja meg egy nyomtatott szöveget tartalmazó kép URL-címét, majd kattintson a **Kép felolvasása** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="read-handwritten-text-handwriting-recognition"></a>Kézzel írt szöveg felolvasása (kézírás-felismerés)

A Computer Vision kézírás-felismerés funkciója kézzel írt szöveget tartalmazó képeket elemez. Az elemzés befejezése után az kézírás-felismerés egy JSON-objektumot hoz létre, amely tartalmazza a szöveget, valamint annak a képen elfoglalt helyét.

Az oktatóalkalmazás kézírás-felismerés funkciójának végrehajtásához tegye a következőket:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Kézírás-felismerés – 1. lépés: Adja hozzá az eseménykezelő kódot az űrlap gombjához

A **handwritingImageButtonActionPerformed** eseménykezelő metódus törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja a **HandwritingImage** metódust a kép elemzéséhez. Amikor a **HandwritingImage** lefut, a metódus formázott JSON-ként jeleníti meg az észlelt szöveget a **Válasz** szövegterületen.

Illessze be az alábbi kódot a **handwritingImageButtonActionPerformed** metódusba.

> [!NOTE]
> A NetBeans nem enged beilleszteni a metódus definíciós sorába (```private void```) vagy az azt záró kapcsos zárójeles kifejezésbe. A kód másolásához másolja le a metódus definíciója és a záró kapcsos zárójeles kifejezés közötti sorokat, és illessze be őket a metódus tartalma fölé.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Kézírás-felismerés – 2. lépés: A REST API-hívás burkolójának hozzáadása

A **HandwritingImage** metódus burkolja a kép elemzéséhez szükséges két REST API-hívást. Mivel a kézírás-felismerés egy időigényes folyamat, kétlépéses megoldást alkalmaz. Az első hívás beküldi a képet feldolgozásra, a második pedig lekéri az észlelt szöveget a feldolgozás után.

A szöveg lekérése után a **HandwritingImage** metódus létrehoz egy **JSONObject** elemet, amely ismerteti a szöveget és annak helyét, vagy **null** értéket eredményez, ha hiba történt.

Illessze be a **HandwritingImage** metódust a **handwritingImageButtonActionPerformed** alá.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occured. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Kézírás-felismerés – 3. lépés: Az alkalmazás futtatása

Az alkalmazás futtatásához nyomja le az **F6** billentyűt. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Kattintson a **Kézzel írott szöveg felolvasása** lapra, adja meg egy kézzel írott szöveget tartalmazó kép URL-címét, majd kattintson a **Kép felolvasása** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="next-steps"></a>További lépések

- [Computer Vision API CC&#35;-oktatóanyag](CSharpTutorial.md)
- [Computer Vision API – Python-oktatóanyag](PythonTutorial.md)
