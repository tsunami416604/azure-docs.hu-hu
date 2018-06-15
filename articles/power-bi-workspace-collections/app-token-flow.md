---
title: Hitelesítése és engedélyezése a Power BI munkaterület gyűjteményekkel |} Microsoft Docs
description: Hitelesítése és engedélyezése a Power BI munkaterület gyűjteményéhez.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 74d34e708fb74daa295642d50643b78af8f6cb7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412185"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Hitelesítése és engedélyezése a Power BI munkaterület gyűjtemények

A Power BI munkaterület gyűjtemények használata **kulcsok** és **alkalmazási jogkivonatok** a hitelesítéshez és engedélyezéshez, explicit végfelhasználói hitelesítés helyett. Ebben a modellben az alkalmazás kezeli a hitelesítési és engedélyezési a végfelhasználók számára. Ha szükséges, az alkalmazás hoz létre, és elküldi a alkalmazási jogkivonatok, amelyek megadják a szolgáltatás a kért jelentés megjelenítéséhez. Ez a kialakítás nincs szükség az alkalmazás Azure Active Directory használandó felhasználói hitelesítéshez és engedélyezéshez, bár továbbra is.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="two-ways-to-authenticate"></a>Történő hitelesítésnek két módja

**Kulcs** -kulcsok az összes Power BI munkaterület gyűjtemények REST API-hívásokhoz használható. A kulcsok megtalálhatók a **Microsoft Azure-portálon** kiválasztásával **összes beállítás** , majd **hívóbetűk**. A kulcs mindig kezelni, mintha azt is, hogy egy jelszót. Ezek a kulcsok engedélye hívható meg egy adott munkaterület-csoportok REST API-k létrehozásához.

A REST-hívást kulcsot használ, adja hozzá a következő engedélyezési fejléc:

    Authorization: AppKey {your key}

**Alkalmazási jogkivonatának** -alkalmazási jogkivonatok használt összes beágyazási kérelem. Úgy lettek kialakítva, ügyféloldali futtatását. A token egyetlen jelentés és az ajánlott lejárati idő beállítása.

Alkalmazás jogkivonatok jwt-t (JSON Web Token), amely alá van írva a kulcsok egyikével.

Az alkalmazás-token a következő jogcímeket tartalmazza:

| Jogcím | Leírás |
| --- | --- |
| **ver** |Az alkalmazás jogkivonatában verzióját. 0.2.0 az aktuális verziója. |
| **és** |A jogkivonat az illetékes címzett. A Power BI munkaterület gyűjtemények használható: "https://analysis.windows.net/powerbi/api." |
| **iss** |Egy karakterlánc, amely az alkalmazást, amely a jogkivonatot ki. |
| **type** |A létrehozandó alkalmazás jogkivonatában típusa. Aktuális az egyetlen támogatott típus van **beágyazása**. |
| **Windows azonnali csatlakozás** |Munkaterület gyűjteménynév a token küldése történik. |
| **belső Windows-adatbázis** |Munkaterületének Azonosítóját a token küldése történik. |
| **a relatív azonosítók** |A token azonosítója küldése történik. |
| **felhasználónév** (nem kötelező) |Az RLS a felhasználónév, típus karakterlánc, amely segítségével azonosítható, a felhasználó RLS szabályok alkalmazásakor. |
| **szerepkörök** (nem kötelező) |Válassza ki a sorszintű biztonság szabályok alkalmazásakor a szerepköröket tartalmazó karakterlánc. Ha több szerepkör, át kell őket, egy karakterlánc-tömbben. |
| **Szolgáltatáskapcsolódási pont** (nem kötelező) |Az engedélyek hatókörök tartalmazó karakterlánc. Ha több szerepkör, át kell őket, egy karakterlánc-tömbben. |
| **Exp** (nem kötelező) |Az az időpont, amelyben a jogkivonat lejár. Az érték Unix időbélyegeket, kell átadni. |
| **NBF** (nem kötelező) |Azt jelzi, hogy a indításakor, amelyben a jogkivonat érvényes alatt. Az érték Unix időbélyegeket, kell átadni. |

Egy minta app tokent néz ki:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Amikor dekódolni, az alábbihoz hasonló:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Nincsenek elérhető az SDK-k, amelyek megkönnyítik a alkalmazási jogkivonatok létrehozása belül módszerek. Például a .NET-hez vessen egy pillantást a [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) osztály és a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) módszerek.

A .NET SDK-ban, olvassa el a [hatókörök](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Hatókörök

Beágyazási jogkivonatok használata esetén előfordulhat, hogy korlátozni szeretné a hozzáférést az erőforrások felhasználásának. Emiatt a hatókörbe tartozó engedélyek jogkivonatot is létrehozhat.

Az alábbiakban a Power BI munkaterület gyűjtemények elérhető hatókört.

|Hatókör|Leírás|
|---|---|
|Dataset.Read|A megadott adatkészlet olvasási engedélye biztosít.|
|Dataset.Write|A megadott adatkészlet írási engedéllyel biztosít.|
|Dataset.ReadWrite|És a megadott adatkészlet olvasási engedélyt biztosít.|
|Report.Read|Itt engedéllyel a megadott jelentés megtekintéséhez.|
|Report.ReadWrite|Engedély megtekintése és szerkesztése a jelentés a megadott biztosít.|
|Workspace.Report.Create|Itt hozhat létre egy új jelentés a megadott munkaterület belül.|
|Workspace.Report.Copy|Megnyit egy meglévő jelentést a megadott munkaterület belül engedélyt biztosít.|

Ekkor megadhatja, hogy több hatókör adhatja például a következő hatókörök közötti használatával.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Szükséges jogcímeket - hatókörök**

Szolgáltatáskapcsolódási pont: {scopesClaim} scopesClaim lehet egy karakterláncot vagy karakterláncok, figyelembe véve az engedélyezett engedélyek munkaterület erőforrások (jelentés, adatkészlet, stb.)

A dekódolt token hatókörökhöz definiált, hasonló lenne:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Műveletek és hatókörök

|Művelet|Célerőforrás|Token engedélyek|
|---|---|---|
|Adatkészlet alapján új jelentés létrehozása (a memóriában).|Adathalmaz|Dataset.Read|
|Adatkészlet alapján új jelentés létrehozása (a memóriában), és mentse a jelentést.|Adathalmaz|* Dataset.Read<br>* Workspace.Report.Create|
|Megtekintheti és (a memória) egy meglévő jelentés felfedezése és szerkesztése. Report.Read Dataset.Read jelenti. Report.Read nem teszi lehetővé a módosítások mentése.|Jelentés|Report.Read|
|Módosítsa és mentse egy meglévő jelentést.|Jelentés|Report.ReadWrite|
|A jelentés (Mentés másként) másolatának mentése.|Jelentés|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Ez a folyamat működése
1. Az API-kulcsokat másolja az alkalmazáshoz. A kulcsok szerezhet be **Azure-portálon**.
   
    ![Hol található az API-kulcsokat az Azure-portálon](media/get-started-sample/azure-portal.png)
1. Token állításokat egy jogcímet, és rendelkezik a lejárati idő.
   
    ![Alkalmazás jogkivonat-folyamata - jogkivonat állításokat jogcím](media/get-started-sample/token-2.png)
1. Token lekérdezi az API elérési kulcsainak aláírva.
   
    ![Alkalmazás jogkivonat-folyamata - token aláírt beolvasása](media/get-started-sample/token-3.png)
1. Felhasználói kérelmek jelentést szeretne megtekinteni.
   
    ![Alkalmazás jogkivonat-folyamata – a felhasználó kéri jelentés megtekintéséhez](media/get-started-sample/token-4.png)
1. Egy API-hozzáférési kulccsal rendelkező token érvényességét.
   
   ![Alkalmazás jogkivonat-folyamata - jogkivonat érvényesítése](media/get-started-sample/token-5.png)
1. A Power BI munkaterület gyűjtemények jelentést küld a felhasználó számára.
   
   ![Alkalmazás jogkivonat-folyamata - szolgáltatás jelentés küldése felhasználónak](media/get-started-sample/token-6.png)

Miután **Power BI munkaterület gyűjtemények** küld jelentést felhasználónak, a felhasználó megtekintheti a jelentést az egyéni alkalmazás. Például, ha az importált a [elemzése forgalmi adatok PBIX minta](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), a minta webalkalmazás alábbihoz hasonlóan fog kinézni:

![Az alkalmazás beágyazott jelentés mintáját](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Lásd még:

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Bevezetés a Microsoft Power BI munkaterület gyűjtemények minta használatába](get-started-sample.md)  
[Microsoft Power BI munkaterület gyűjtemények gyakori helyzetek](scenarios.md)  
[Ismerkedés a Microsoft Power BI munkaterület gyűjtemények](get-started.md)  
[A csharp nyelvű Power bi Git-tárház](https://github.com/Microsoft/PowerBI-CSharp)

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)