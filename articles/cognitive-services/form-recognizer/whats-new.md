---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Form Recognizer API legújabb módosításait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155171"
---
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

Az Űrlapfelismerő szolgáltatás folyamatosan frissül. Ebben a cikkben naprakész maradhat a szolgáltatásfejlesztésekkel, javításokkal és dokumentációfrissítésekkel.

> [!NOTE]
> A rövid útmutatók és útmutatók űrlapfelismerő mindig az API legújabb verzióját használja, kivéve, ha meg van adva.

## <a name="march-2020"></a>2020. március 

### <a name="extraction-enhancements"></a>Kivonási fejlesztések

Ez a kiadás magában foglalja a kibontási fejlesztések és a pontosság javítása, különösen a képesség, hogy a címke és kivonat több kulcs/érték pár ugyanabban a szövegsorban. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Az űrlapfelismerő mintacímkéző eszköz mostantól nyílt forráskódú

Az Űrlapfelismerő mintacímkéző eszköz már nyílt forráskódú projektként is elérhető. Integrálhatja azt a megoldásaiba, és igény szerint módosíthatja az ügyfélspecifikus módosításokat.

Az űrlapfelismerő mintacímkéző eszközről a [GitHubon](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)elérhető dokumentációban olvashat bővebben.

### <a name="labeling-value-types"></a>Címkézési értéktípusok

Az értéktípusok már használhatók az Űrlapfelismerő mintacímkéző eszközével. Ezek az értéktípusok jelenleg támogatottak: 

* Sztring
* Szám 
* Egész szám
* Dátum 
* Time

Ez a kép azt mutatja, hogy az Űrlapfelismerő mintacímkéző eszközén milyen értéktípus-kijelölés jelenik meg:

> [!div class="mx-imgBorder"]
> ![Értéktípus-kijelölés mintacímkéző eszközzel](./media/whats-new/formre-value-type.png)

A kibontott táblázat a JSON `pageResults`kimenetben érhető el.

### <a name="table-visualization"></a>Táblázatos megjelenítés 

Az Űrlapfelismerő címkéző eszköz mostantól megjeleníti a dokumentumban felismert táblázatokat. Ez lehetővé teszi a dokumentumból felismert és kinyert táblák megtekintését az Űrlapfelismerő mintacímkéző eszközzel való címkézés és elemzés előtt. Ez a funkció a rétegek beállítással kapcsolható be és ki. 

Ez egy példa a táblák felismerésére és kibontására:

> [!div class="mx-imgBorder"]
> ![Táblázatos megjelenítés a mintacímkéző eszközzel](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> A címkék címkézése nem támogatott. Ha a táblákat nem ismeri fel és nem személyesíti meg automatikusan, akkor csak kulcs/érték párokként címkézheti őket. Amikor a táblázatokat kulcs/érték párként címkézi, kérjük, címkézze az egyes cellákat értékként.

### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése

* A TLS 1.2 mostantól a szolgáltatáshoz érkező összes HTTP-kérelemre érvényes. További információ: [Azure Cognitive Services security.](../cognitive-services-security.md)

## <a name="january-2020"></a>2020. január

Ez a kiadás bemutatja a Form Recognizer 2.0 (előzetes verzió). Az alábbi szakaszokban további információkat talál az új funkciókról, fejlesztésekről és módosításokról. 

### <a name="new-features"></a>Új funkciók

* **Egyéni modell**
  * **Vonat címkékkel** Most már betaníthat egy egyéni modellt manuálisan címkézett adatokkal. Ez jobban teljesítő modelleket eredményez, és olyan modelleket képes előállítani, amelyek összetett űrlapokkal vagy kulcsok nélküli értékeket tartalmazó űrlapokkal dolgoznak.
  * **Aszinkron API** Az aszinkron API-hívások segítségével nagy adatkészletekkel és fájlokkal taníthatja be és elemezheti azokat.
  * **TIFF-fájl támogatása** Most már betanítása és kivonat adatok TIFF dokumentumokat.
  * **A kivonás pontosságának javítása**

* **Előre összeállított bevételezési modell**
  * **Tipp összegek** Most már kivonat tipp összegeket és más kézzel írt értékeket.
  * **Sorkibontás** A sorértékeket kivonhatja a bevételezésekből.
  * **Megbízhatósági értékek** Megtekintheti a modell megbízhatóságát az egyes kinyert értékekhez.
  * **A kivonás pontosságának javítása**

* **Elrendezés kihúzása** Most már használhatja az Elrendezés API-t szöveges adatok és táblázatadatok kinyerésére az űrlapokból.

### <a name="custom-model-api-changes"></a>Egyéni modell API-módosítások

A betanításhoz és az egyéni modellek használatához használt összes API-t átnevezték, és néhány szinkron metódus most antól aszinkron. A következő jelentős változások:

* A modell betanítási folyamata most aszinkron. Az **/custom/models** API-hívással kezdeményezhet betanítást. Ez a hívás egy műveletazonosítót ad vissza, amelyet **az egyéni/modellek/{modelID}** függvénybe adhat át a betanítási eredmények visszaadása érdekében.
* A kulcs/érték kinyerését most az **/custom/models/{modelID}/analyze** API-hívás kezdeményezi. Ez a hívás egy műveletazonosítót ad vissza, amelyet **az egyéni/modellek/{modelID}/analyzeResults/{resultID}** függvénybe adhat át a kibontási eredmények visszaadása érdekében.
* A Betanítás művelet műveletazonosítói most már a HTTP-válaszok **Hely** fejlécében találhatók, nem pedig a **Művelet-hely** fejlécben.

### <a name="receipt-api-changes"></a>Bevételezési API-módosítások

A bevételi beérkezések olvasására szolgáló API-k át lettek nevezve.

* A bevételezési adatok kinyerését most a **/prebuilt/receipt/analyze** API hívás kezdeményezi. Ez a hívás egy műveletazonosítót ad vissza, amelyet a **/prebuilt/receipt/analyzeResults/{resultID}** értékbe adhat át a kibontási eredmények visszaadásához.

### <a name="output-format-changes"></a>Kimeneti formátum módosítása

Az összes API-hívás JSON-válaszai új formátumokkal rendelkeznek. Egyes kulcsok és értékek hozzáadva, eltávolítva vagy átnevezve lettek. Tekintse meg a rövid útmutatók példákat az aktuális JSON-formátumok.

## <a name="next-steps"></a>További lépések

Az [űrlapfelismerő API-k](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)első lépésekhez rövid [útmutatót](quickstarts/curl-train-extract.md) kell végeznie.