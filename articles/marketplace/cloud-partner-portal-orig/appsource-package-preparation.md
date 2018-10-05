---
title: Appsource-ban csomag előkészítése |} A Microsoft Docs
description: A hogyan készítheti elő és az appsource-ban csomagok Explanaion.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0b24c5d2f174c9a656e81d0c85e12b589d7d7799
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810647"
---
# <a name="appsource-package-preparation"></a>Appsource-ban csomag előkészítése

Egy solution.zip fájl mellett szüksége lesz egy **AppSource csomag**. Ez az egy .zip fájlt, amely tartalmazza az összes olyan eszköz, a megoldás üzembe helyezése az ügyfelek CRM a környezetre, a folyamat automatizálásához szükséges. A **AppSource csomag** a következő összetevőket tartalmazza

* A Package deployerhez csomag
* **Content_Types.xml** fájlt az eszközöket használhatja
* XML-fájlt az alkalmazás-specifikus adatokkal
* jelenítse meg a felügyeleti központban a listaelem 32 x 32-embléma
* A licencfeltételeket, adatvédelmi szabályzat

Az alábbi lépéseket követve létrehozhat az AppSource-csomagot.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Hozzon létre egy csomagot a Package deployerhez

A csomag, a Package deployerhez az AppSource-csomag egy részét képezi.

A Package deployerhez csomagot hozhat létre, kövesse az alábbi utasításokat: [ https://msdn.microsoft.com/library/dn688182.aspx ](https://msdn.microsoft.com/library/dn688182.aspx). Amikor végzett, a csomagot az alábbi eszközök állnak:

1. Kódcsomag mappája: tartalmazza az összes megoldáshoz, konfigurációs adatokat, egybesimított fájlok és a csomag tartalmát. _Megjegyzés: A következő példában indulunk ki a csomag mappájából "PkgFolder" nevezzük._
2. DLL-je: A szerelvény tartalmazza a csomaghoz tartozó egyéni kódot. _Megjegyzés: A következő példában indulunk ki a fájl neve "MicrosoftSample.dll."_

Most hozzon létre egy nevű fájlt kell "**Content_Types.xml**" Ez a fájl minden, a csomag részét képező eszközök bővítmény listázza. A következő példakód a fájlt.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

A végső lépés a zip-fájl a fájlban a következő szolgál. Meghívására **package.zip**. Tartalmazni fog

1. PkgFolder (beleértve a mappán belül minden)
2. DLL-fájl
3. **Content_Types.XML**

Package.zip létrehozásához szükséges lépéseket:

1. A csomag mappáját, és helyezze **Content_Types.xml** fájlt, és a egy könyvtárba PackageName.dll.

![CRMScreenShot2](media/CRMScreenShot2.png)

2. Válassza ki az összes elem a mappában, kattintson a jobb gombbal, és küldjön a tömörített (zip) mappa kiválasztása

![CRMScreenShot3](media/CRMScreenShot3.png)

3. Módosítsa a nevét package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Az AppSource-csomag létrehozása

Az appsource-ban csomaghoz szükséges néhány további fájlokat.

1. JPG (32 x 32 megoldás)
2. HTML (HTML formátumú fájl)
3. **Content_Types.xml** (ugyanaz, mint fent)
4. xml

Itt látható példa kód input.xml. A definíciók lásd az alábbi táblázatban.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>http://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Ahol:**

|Mező|Részletek|
|---|---|
|ProviderName|A megoldás érkező ki. Ha a Microsoft csapata Microsoft kell lennie.|
|PackageFile |Csomag és a egy tartalom zip Deployer eszközök\_types.xml fájlt. A zip-fájlt tartalmaznia kell a package deployer szerelvényt és a mappa package deployer eszközökre. Vagyis package.zip|
|SolutionAnchorName |A megoldás a package deployerhez a megjelenített nevét és leírását a megoldás eszközök szolgálja ki a zip-fájl neve.|
| Kezdődátum| Ez az a dátum, amelyen a megoldáscsomag válnak elérhetővé. A formátuma nem hh/nn/éééé|
|endDate|Ez az a dátum, hogy a megoldás csomag le fog állni legyenek elérhetők. A formátuma nem hh/nn/éééé |
|SupportedCountries |Ez az egy vesszővel tagolt listája, azon országok, amely ezt a csomagot kell megjelennie. Lépjen kapcsolatba az online szolgáltatások összes aktuális országkódok listáját. Amikor, ez a lista írása volt: AE, AL, AM, AO, AR, AT, Ausztrália, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, tényleges súly, CY, CZ, DE, DK, DO, DZ, EC, EE, EG , ES FI, FR, GB, A GE, GH, GR, GT, HK, HN, HR, HU, -AZONOSÍTÓ, INTERNET EXPLORER, IL, IN, IQ,, AZT, JM, JO, JP, KE, KG, KN, KOREA, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MINŐSÍTÉSSEL, MC, MD, NEKEM, MK, MN, HÓNAP, FŐ CÉLKISZOLGÁLÓ, MU, MX, SAJÁT , NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PULL-KÉRELEM, PS, CSENDES-ÓCEÁNI IDŐ, PY, QA, RO, RS, RU, RW, SA, KI, SG, SI, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA, EGYESÜLT ÁLLAMOK, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | A további-információs oldalt a csomag URL-címe. |
|Területi beállítások|Ebben a csomópontban az előnyben részesített megoldás UX a támogatni kívánt UX nyelvenként egy példánya Ez a csomópont tartalmazza, amelyek ismertetik a területi beállítás, a embléma és az egyes nyelvekhez gyermekek|
|Területi beállítások: PackageLocale.Code|Ebben a csomópontban a nyelv LCID. Példa: Amerikai angol érték 1033|
|Területi beállítások: PackageLocale.IsDefault|Azt jelzi, hogy ez az alapértelmezett nyelvet. Ez csökkenése használatos biztonsági nyelv, ha az ügyfél által választott UX nyelve nem érhető el.|
|Területi beállítások: embléma|Ez Ha az embléma szeretné a csomaghoz használni. Ikon mérete 32 x 32. Engedélyezett formátumok a következők PNG és JPG|
|Területi beállítások: használati: PackageTerm.File|Ez a fájl nevét a HTML-dokumentumot, amely tartalmazza a licencfeltételeket.|

Itt látható, ahol megjelenik a portálon:

![CRMScreenShot5](media/CRMScreenShot5.png)

A végső lépés a zip-fájl a fájlban a következő szolgál.

1. a zip (létrehozott)
2. **Content_Types.XML**
3. xml
4. PNG
5. HTML

![CRMScreenShot6](media/CRMScreenShot6.png)

Nevezze át a fájlt az alkalmazás megfelelően. Mi inkább, hogy tartalmazzák a cég nevét és az alkalmazás neve. Például: **_Microsoft_SamplePackage.zip**.
