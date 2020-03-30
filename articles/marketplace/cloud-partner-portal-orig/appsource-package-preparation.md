---
title: AppSource csomag előkészítése | Azure Piactér
description: Explanaion hogyan kell elkészíteni és építeni AppSource csomagok .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280609"
---
# <a name="appsource-package-preparation"></a>AppSource csomag előkészítése

A solution.zip fájl mellett **AppSource-csomagra**is szüksége lesz. Ez egy .zip fájl, amely tartalmazza az összes olyan eszközt, amely a megoldás ügyfelek CRM-környezetbe való üzembe helyezésének automatizálásához szükséges. Az **AppSource-csomag** a következő összetevőket tartalmaz:

* Csomag a csomagtelepítőszámára
* **Content_Types.xml** fájl a használt kellékekkel
* xml-fájl alkalmazásspecifikus adatokkal
* 32x32-es embléma, amely a listával együtt jelenik meg a Felügyeleti központban
* Licencfeltételek, adatvédelmi szabályzat

Az alábbi lépések segítenek az AppSource-csomag létrehozásában.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Csomag létrehozása a csomagtelepítő számára

A csomag telepítő csomagja az AppSource-csomag egyik része.

Csomag létrehozásához a csomagtelepítőhöz kövesse az [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)alábbi utasításokat: . Ha elkészült, a csomag az alábbi eszközökből áll:

1. Csomagmappa: tartalmazza az összes megoldást, konfigurációs adatot, egyablakos fájlokat és a csomag tartalmát. _Megjegyzés: A következő példában azt feltételezzük, hogy a csomag mappáját "PkgFolder" -nak nevezzük._
2. dll: A szerelvény tartalmazza a csomag egyéni kódját. _Megjegyzés: Az alábbi példában azt feltételezzük, hogy ezt a fájlt "MicrosoftSample.dll" fájlnak nevezzük._

Most létre kell hoznia egy "**Content_Types.xml**" nevű fájlt: Ez a fájl felsorolja a csomag részét képezi összes eszközbővítményt. Itt van a fájl példakódja.

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

Az utolsó lépés az, hogy zip a következő egy fájlba. Nevezzük **package.zip**. Ez tartalmazni fogja

1. PkgFolder (beleértve mindent a mappában)
2. Dll
3. **Content_Types.xml**

A package.zip létrehozásának lépései:

1. A csomagmappát, **a Content_Types.xml** fájlt és a PackageName.dll fájlt helyezze egy könyvtárba.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Jelölje ki a mappa összes elemét, kattintson a jobb gombbal, és válassza a Küldés tömörített (zip) mappába parancsot

![CRMScreenShot3](media/CRMScreenShot3.png)

1. A név módosítása package.zip névre

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. AppSource-csomag létrehozása

Az AppSource-csomaghoz néhány további fájl szükséges.

1. jpg (32x32-es felbontás)
2. html (HTML formátumú fájl)
3. **Content_Types.xml** (ugyanaz, mint fent)
4. xml

Íme az input.xml példakódja. Lásd a definíciókat az alábbi táblázatban.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Az elemek magyarázata:**

|Mező|Részletek|
|---|---|
|Szolgáltatóneve|Ki a megoldás jön. Ha egy Microsoft-csapat, akkor ennek a Microsoftnak kell lennie.|
|PackageFile (csomagfájl) |A package deployer eszközök egy\_content types.xml fájllal együtt vannak tömörítve. Ennek a zip-fájlnak tartalmaznia kell a csomagtelepítő szerelvényt, és a csomagot üzembe helyező eszközöket tartalmazó mappát. Azaz, package.zip|
|SolutionAnchorName (Megoldás: horgonyzó név) |A megoldás zip-fájljának neve a csomagtelepítőben, amely a megoldáseszközök megjelenítendő nevéhez és leírásához használatos.|
| Kezdő dátum| Ez az a dátum, amikor a megoldáscsomag elérhetővé válik. A formátum: MM/DD/YYYY|
|Záró dátum|Ez az a dátum, amikor a megoldáscsomag nem lesz elérhető. A formátum: MM/DD/YYYY |
|Támogatott országok |Ez egy vesszővel tagolt lista azokról az országokról/régiókról, amelyeknek látniuk kell ezt a csomagot. Az aktuális országkódok listájáért forduljon az online szolgáltatásokhoz. Abban az időben, az írás a lista: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK, DO, DZ, EC, EE, EG, ,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,Ky,KZ,LB,Lk,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,ÉN NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, PY, QA, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA, US, UY, UZ, VI, VN, ZA, ZW |
|Tudjon megtöbbLink | URL-címet a csomag további információs oldalára. |
|Helyszínek|Ennek a csomópontnak egy példánya minden olyan UX-nyelvhez, amelyet támogatni szeretne a preferált megoldás felhasználói felületén. Ez a csomópont olyan gyermekeket tartalmaz, akik leírják az egyes nyelvek területi beállítását, emblémáját és kifejezéseit.|
|Területi beállítások: PackageLocale.Code|A csomópont nyelvének LCID azonosítója. Példa: Amerikai angol 1033|
|Területi beállítások: PackageLocale.IsDefault|Azt jelzi, hogy ez az alapértelmezett nyelv. Ez tartalék nyelvként használatos, ha az ügyfél által választott ux-nyelv nem érhető el.|
|Területi beállítások: Embléma|Ez akkor jelenik meg, ha a csomaghoz használni kívánt embléma. Az Icon mérete 32x32. Az engedélyezett formátumok a PNG és a JPG|
|Területi beállítások:Feltételek: PackageTerm.File|Ez annak a HTML-dokumentumnak a fájlneve, amely tartalmazza a licencfeltételeket.|

Itt van, ahol a logó jelenik meg:

![CRMScreenShot5](media/CRMScreenShot5.png)

Az utolsó lépés az, hogy zip a következő egy fájlba.

1. zip (korábban létrehozva)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Nevezze át a fájlt az alkalmazásnak megfelelő re. Azt javasoljuk, hogy adja meg a cég nevét és az alkalmazás nevét. Például: **_Microsoft_SamplePackage.zip**.
