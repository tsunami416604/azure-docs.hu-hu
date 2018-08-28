---
title: Hitelesítés és engedélyezés a Power BI-Munkaterületcsoportok |} A Microsoft Docs
description: Hitelesítés és engedélyezés a Power BI munkaterületi gyűjtemények.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 54eefc9c488718dae25e35ef0c5f0f5c50dea16c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041741"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Hitelesítés és engedélyezés a Power BI munkaterületi gyűjteményekkel

A Power BI-Munkaterületcsoportok használata **kulcsok** és **alkalmazási jogkivonatok** a hitelesítéshez és engedélyezéshez, explicit végfelhasználói hitelesítés helyett. Ebben a modellben az alkalmazás kezeli a hitelesítési és engedélyezési a végfelhasználók számára. Ha szükséges, az alkalmazás hoz létre, és az alkalmazás-jogkivonatokról, mondja el szolgáltatásunkat, amellyel jelennek meg a kért jelentést küld. Ez a kialakítás nincs szükség az alkalmazások a felhasználó hitelesítésének és engedélyezésének, az Azure Active Directory, bár továbbra is.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="two-ways-to-authenticate"></a>Kétféleképpen hitelesítéséhez

**Kulcs** -kulcsokat is használhat az összes Power BI munkaterületi gyűjtemények REST API-hívások. A kulcsok megtalálható a **Microsoft Azure-portálon** kiválasztásával **minden beállítás** , majd **hozzáférési kulcsok**. A kulcs mindig kezelje, mintha ez egy jelszót. Ezek a kulcsok engedélye, hogy bármilyen REST API-hívást indít az egy adott munkaterület-csoport.

A REST-hívást kulcsot használ, adja hozzá a következő engedélyeztetési fejléc:

    Authorization: AppKey {your key}

**Alkalmazás-jogkivonatára** -beágyazási irányuló kérések alkalmazási jogkivonatok használhatók. Ügyféloldali futtatásra lettek kialakítva. A jogkivonat korlátozódik egyetlen jelentést és az ajánlott eljárás a lejárati idő beállítása.

Alkalmazás-jogkivonatokról a jwt-t (JSON Web Token), a kulcsok egyikét aláírva.

Az alkalmazás-jogkivonatára tartalmazza a következő jogcímek:

| Jogcím | Leírás |    
| --- | --- |
| **ver** |Az alkalmazás-jogkivonatára verziója. 0.2.0 van a jelenlegi verziót. |
| **AUD** |A jogkivonat az illetékes címzett. A Power BI-Munkaterületcsoportok használatával: *https:\//analysis.windows.net/powerbi/api*. |
| **iss** |Egy karakterlánc, amely az alkalmazás, amely kiállította a jogkivonatot. |
| **type** |A létrehozott alkalmazás-jogkivonatára típusa. Aktuális az egyetlen támogatott típus: **beágyazási**. |
| **Windows azonnali csatlakozás** |A munkaterületi gyűjtemény nevének a jogkivonat kiállítása az folyamatban van. |
| **WID-topológiával** |Munkaterület-Azonosítót a jogkivonat kiállítása az folyamatban van. |
| **a relatív azonosítók** |A jogkivonat Jelentésazonosító küldése történik. |
| **felhasználónév** (nem kötelező) |Az rls-t használja, felhasználónév egy karakterláncérték, amely segítségével azonosítható a felhasználó az RLS-szabályok alkalmazásakor. |
| **szerepkörök** (nem kötelező) |A sorszintű biztonsági szabályok alkalmazásakor kiválasztható szerepköröket tartalmazó karakterlánc. Több szerepkör átadásakor kell azokat átadni, egy karakterlánc-tömbben. |
| **Szolgáltatáskapcsolódási pont** (nem kötelező) |Az engedélyek hatókörök tartalmazó karakterlánc. Több szerepkör átadásakor kell azokat átadni, egy karakterlánc-tömbben. |
| **Exp** (nem kötelező) |Az időpont, amelyben a jogkivonat lejár. Az érték Unix időbélyegeket, kell átadni. |
| **NBF** (nem kötelező) |Azt jelzi, hogy a indításakor, amelyben a jogkivonat érvényes folyamatban. Az érték Unix időbélyegeket, kell átadni. |

Egy mintául szolgáló alkalmazás-jogkivonatára hasonlóan néz ki:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Amikor dekódolni, következőhöz hasonló lesz:

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

Nincsenek elérhető belül az SDK-k, hogy az alkalmazás-jogkivonatokról létrehozása egyszerűbben módszereket. Ha például a .NET-hez tekintse meg a [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) osztály és a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) módszereket.

A .NET SDK-ban, olvassa el [hatókörök](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Hatókörök

Beágyazási token használatakor előfordulhat, hogy korlátozni szeretné a erőforrások hozzáférést biztosít. Ebből kifolyólag a jogkivonatot az hatókörbe tartozó engedélyeket is létrehozhat.

Az alábbiakban a Power BI-Munkaterületcsoportok elérhető hatókörök.

|Hatókör|Leírás|
|---|---|
|Dataset.Read|A megadott adatkészlet olvasási engedélyt biztosít.|
|Dataset.Write|Itt a megadott adatkészlet írási engedéllyel.|
|Dataset.ReadWrite|És a megadott adatkészlet olvasási engedélyt biztosít.|
|Report.Read|A megadott megtekintési engedélyt biztosít.|
|Report.ReadWrite|Itt megtekintheti és szerkesztheti a jelentés a megadott engedély.|
|Workspace.Report.Create|Itt egy új jelentés a megadott munkaterület belül létrehozásához szükséges engedéllyel.|
|Workspace.Report.Copy|Klónozásához egy meglévő jelentés a megadott munkaterület belül engedélyt biztosít.|

Egy hely között az alábbiakhoz hasonlóan a hatókörök használatával több hatókörhöz is megadhatja.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Szükséges jogcímek - hatókörök**

SCP-je: {scopesClaim} scopesClaim karakterláncot vagy karakterláncok, figyelembe véve az engedélyezett engedélyeket (jelentés, adatkészlet, stb.) a munkaterület erőforrásai is lehet.

A dekódolt jogkivonat hatókörökkel definiált, ehhez hasonlóan néz ki a:

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

### <a name="operations-and-scopes"></a>Műveletek és -hatókörök

|Művelet|Célerőforrás|Token engedélyek|
|---|---|---|
|(Memórián belüli) hozzon létre egy új jelentés, adatkészlet alapján.|Adathalmaz|Dataset.Read|
|Egy adatkészleten alapuló új jelentés létrehozása (memórián belüli), majd mentse a jelentést.|Adathalmaz|* Dataset.Read<br>* Workspace.Report.Create|
|Megtekintheti és (memórián belüli) egy meglévő jelentés tallózása és szerkesztése. Report.Read Dataset.Read jelenti. Report.Read nem teszi lehetővé a módosítások mentése.|Jelentés|Report.Read|
|Szerkessze, és a egy meglévő jelentés mentése.|Jelentés|Report.ReadWrite|
|Mentse a jelentés (Mentés másként).|Jelentés|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Itt látható a folyamat működése
1. Az alkalmazás API-kulcs másolása. A kulcsok kezdheti **az Azure portal**.
   
    ![API-kulcs megkeresése az Azure Portalon](media/get-started-sample/azure-portal.png)
1. Token használjon esetleg imperatív állításokat egy jogcímet, és a lejárati időt tartalmaz.
   
    ![Alkalmazási jogkivonatok folyamata - jogkivonat használjon esetleg imperatív állításokat jogcím](media/get-started-sample/token-2.png)
1. Jogkivonat-API elérési kulcsainak beolvasása aláírva.
   
    ![Alkalmazási jogkivonatok folyamata - jogkivonat aláírása beolvasása](media/get-started-sample/token-3.png)
1. Felhasználói kérelmek jelentés megtekintéséhez.
   
    ![Alkalmazási jogkivonatok folyamata – a felhasználó kéri egy jelentés megtekintése](media/get-started-sample/token-4.png)
1. Jogkivonat-API elérési kulcsainak az érvényességét.
   
   ![Alkalmazási jogkivonatok folyamata - jogkivonat érvényesítése](media/get-started-sample/token-5.png)
1. A Power BI-Munkaterületcsoportok jelentést küld a felhasználónak.
   
   ![Alkalmazási jogkivonatok folyamata - szolgáltatás küldhet jelentés felhasználó](media/get-started-sample/token-6.png)

Miután **Power BI-Munkaterületcsoportok** küld a felhasználónak, a felhasználó a jelentés az egyéni alkalmazás tekinthetik meg a jelentést. Például, ha importálta a [elemzése értékesítési adatok PBIX-minta](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), a mintául szolgáló webalkalmazás ehhez hasonlóan néz ki:

![Minta beágyazott alkalmazás jelentés](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Lásd még:

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Ismerkedés a Microsoft Power BI-Munkaterületcsoportok minta](get-started-sample.md)  
[A Microsoft Power BI-Munkaterületcsoportok gyakori helyzetek](scenarios.md)  
[Ismerkedés a Microsoft Power BI munkaterületi gyűjteményekkel](get-started.md)  
[A Power bi-CSharp Git-adattár](https://github.com/Microsoft/PowerBI-CSharp)

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)