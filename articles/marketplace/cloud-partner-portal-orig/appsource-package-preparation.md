---
title: AppSource csomag előkészítése | Azure piactér
description: Explanaion a AppSource-csomagok előkészítéséhez és létrehozásához.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280609"
---
# <a name="appsource-package-preparation"></a>AppSource csomag előkészítése

A Solution. zip fájl mellett egy **AppSource-csomagra**is szüksége lesz. Ez egy. zip-fájl, amely tartalmazza az összes olyan eszközt, amely a megoldásnak az ügyfelek CRM-környezetbe való üzembe helyezésének automatizálásához szükséges. A **AppSource csomag** a következő összetevőket tartalmaz

* Csomag telepítője
* **Content_Types. XML** fájl, amely a használt eszközöket használja
* XML-fájl az alkalmazás-specifikus adataival
* 32x32-embléma, amely a felügyeleti központban megjelenik a listával
* Licencfeltételek, adatvédelmi szabályzat

Az alábbi lépések segítséget nyújtanak a AppSource-csomag létrehozásában.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Csomag létrehozása a csomag-telepítőhöz

A csomag-telepítő csomagja a AppSource csomag egyik része.

Ha csomagot szeretne létrehozni a csomagkezelő telepítéséhez, kövesse az alábbi utasításokat: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Ha elkészült, a csomag az alábbi eszközökből áll:

1. Csomag mappája: a csomag összes megoldását, konfigurációs adatait, egyszerű fájlját és tartalmát tartalmazza. _Megjegyzés: az alábbi példában feltételezzük, hogy a csomag mappájának neve "PkgFolder"._
2. DLL: a szerelvény tartalmazza a csomaghoz tartozó egyéni kódot. _Megjegyzés: az alábbi példában feltételezzük, hogy a fájl neve "MicrosoftSample. dll" lesz._

Most létre kell hoznia egy "**Content_Types. XML**" nevű fájlt, amely tartalmazza a csomag részét képező összes eszköz-bővítményt. Íme a fájlhoz tartozó példa kódja.

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

Az utolsó lépés az, hogy a következőt egy fájlba zip-fájlként. Hívja meg a **Package. zip fájlt**. Ez tartalmazni fogja

1. PkgFolder (beleértve a mappában található összeset)
2. DLL
3. **Content_Types. XML**

A Package. zip létrehozásának lépései:

1. Helyezze a Package mappát, **Content_Types. XML** fájlt és a PackageName. dll fájlt egy könyvtárba.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Válassza ki az összes elemet a mappában, kattintson a jobb gombbal, és válassza a Küldés tömörített (zip) mappába lehetőséget.

![CRMScreenShot3](media/CRMScreenShot3.png)

1. A név módosítása a Package. zip névre

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. AppSource-csomag létrehozása

A AppSource-csomaghoz néhány további fájl szükséges.

1. jpg (32x32-feloldás)
2. HTML (HTML formátumú fájl)
3. **Content_Types. XML** (megegyezik a fentivel)
4. xml

Az alábbi példa a input. xml fájl kódját írja le. Lásd az alábbi táblázatban szereplő definíciókat.

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
 
**Ahol**

|Mező|Részletek|
|---|---|
|ProviderName|Kitől érkezik a megoldás. Ha egy Microsoft-csapat, akkor a Microsoftnak kell lennie.|
|PackageFile |A csomag-üzembe helyezési eszközök a Content\_types. xml fájllal együtt tömörítettek. A zip-fájlnak tartalmaznia kell a csomag-telepítő szerelvényt, valamint a csomagot, amely tartalmazza a csomag-üzembe helyezési eszközöket. Ez a csomag. zip|
|SolutionAnchorName |A megoldás zip-fájljának neve a csomag-telepítőben, amelyet a rendszer a megoldási eszközök megjelenítendő nevére és leírására használ.|
| StartDate| Ez az a dátum, amikor a megoldási csomag elérhetővé válik. A formátum HH/NN/ÉÉÉÉ.|
|EndDate|Ez az a dátum, ameddig a megoldási csomag elérhetővé válik. A formátum HH/NN/ÉÉÉÉ. |
|SupportedCountries |Ez egy vesszővel tagolt lista azon országok/régiók számára, amelyeknek ezt a csomagot látniuk kell. Az aktuális országkódok listájáért forduljon a online szolgáltatásokhoz. A lista írásakor a következő volt: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK, DO, DZ, EC, EE, pl., ES, FI, FR, GB, GE, GH, GR, GT, HK, HN, HR , IN, IQ, IS, IT, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, MO, MT, MU, MX, SAJÁT, NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, A., A QA, A RO, AZ RS, AZ RU, AZ RW, AZ SA, AZ SE, A SG, A. , TR, TT, TW, UA, EGYESÜLT ÁLLAMOK, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | A csomag további információ oldalának URL-címe. |
|Helyszíneken|A csomópont egy példánya az előnyben részesített megoldásban támogatni kívánt összes UX nyelvhez. Ez a csomópont olyan gyermekeket tartalmaz, amelyek leírják az egyes nyelvek területi beállítását, emblémáját és kifejezéseit.|
|Területi beállítások: PackageLocale. code|A csomópont nyelvének LCID-je. Példa: amerikai angol, 1033|
|Területi beállítások: PackageLocale. IsDefault|Azt jelzi, hogy ez az alapértelmezett nyelv. Ezt használja a rendszer visszaesési nyelvként, ha az ügyfél által választott UX nyelv nem érhető el.|
|Területi beállítások: embléma|Ez a csomaghoz használni kívánt embléma. Az ikon mérete 32x32. Az engedélyezett formátumok a PNG és a JPG|
|Területi beállítások: feltételek: PackageTerm. file|Ez a licencszerződést tartalmazó HTML-dokumentum fájlneve.|

Itt látható a logó:

![CRMScreenShot5](media/CRMScreenShot5.png)

Az utolsó lépés az, hogy a következőt egy fájlba zip-fájlként.

1. zip (korábban létrehozva)
2. **Content_Types. XML**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Nevezze át a fájlt az alkalmazásának megfelelőre. Javasoljuk, hogy adja meg a vállalat nevét és az alkalmazás nevét. Például: **_Microsoft_SamplePackage. zip**.
