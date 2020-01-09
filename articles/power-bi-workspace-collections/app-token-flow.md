---
title: Power BI munkaterület-gyűjtemények hitelesítése és engedélyezése
description: Power BI munkaterület-gyűjtemények hitelesítése és engedélyezése.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427117"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Power BI munkaterület-gyűjtemények hitelesítése és engedélyezése

Power BI munkaterület-gyűjtemények **kulcsokat** és **alkalmazás-jogkivonatokat** használnak a hitelesítéshez és engedélyezéshez, explicit végfelhasználói hitelesítés helyett. Ebben a modellben az alkalmazás kezeli a végfelhasználók hitelesítését és engedélyezését. Ha szükséges, az alkalmazás létrehozza és elküldi azokat az alkalmazás-jogkivonatokat, amelyek közlik a szolgáltatással a kért jelentés megjelenítését. Ehhez a kialakításhoz nem szükséges, hogy az alkalmazás használja a Azure Active Directory felhasználói hitelesítéshez és engedélyezéshez, de továbbra is.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="two-ways-to-authenticate"></a>Kétféle hitelesítés

**Kulcs** – az összes Power bi-munkaterület-gyűjteményhez használhat kulcsokat REST API hívásokhoz. A kulcsok megtalálhatók a **Microsoft Azure Portalban** az **összes beállítás** kiválasztásával, majd a **kulcsok elérésével**. A kulcsot mindig úgy kezelje, mintha a jelszó. Ezeknek a kulcsoknak van engedélye arra, hogy REST API hívást végezzen egy adott munkaterület-gyűjteményen.

Ha egy REST-híváshoz szeretne kulcsot használni, adja hozzá a következő engedélyezési fejlécet:

    Authorization: AppKey {your key}

**Alkalmazás-jogkivonat** – az alkalmazás-jogkivonatok az összes beágyazási kérelemhez használatosak. Úgy tervezték, hogy az ügyféloldali futtatásra legyenek kialakítva. A jogkivonat egyetlen jelentésre korlátozódik, és az ajánlott eljárás a lejárati idő beállítása.

Az alkalmazás-jogkivonatok az egyik kulcs által aláírt JWT (JSON Web Token).

Az alkalmazás-jogkivonat a következő jogcímeket tartalmazza:

| Jogcím | Leírás |    
| --- | --- |
| **ver** |Az alkalmazás-jogkivonat verziója. a 0.2.0 az aktuális verzió. |
| **AUD** |A jogkivonat címzettjei. Power BI munkaterület-gyűjtemények esetén használja a következőt: *https:\//Analysis.Windows.net/powerbi/API*. |
| **ISS** |A jogkivonatot kiállító alkalmazást jelző karakterlánc. |
| **type** |A létrehozandó alkalmazás-jogkivonat típusa. Jelenleg az egyetlen támogatott típus a **beágyazás**. |
| **WCN** |A munkaterület-gyűjtemény neve, amelybe a jogkivonat ki van állítva. |
| **wid** |A munkaterülethez tartozó azonosító, amelynek a tokenje ki van állítva. |
| **RID** |A jelentés azonosítója, amelybe a jogkivonat ki van állítva. |
| **username** (nem kötelező) |Az RLS használatával a username egy karakterlánc, amely segíthet azonosítani a felhasználót az RLS-szabályok alkalmazása során. |
| **szerepkörök** (nem kötelező) |A sor szintű biztonsági szabályok alkalmazása során kiválasztható szerepköröket tartalmazó sztring. Ha egynél több szerepkört továbbít, azokat Csípés tömbként kell átadni. |
| **szolgáltatáskapcsolódási** pont (nem kötelező) |Az engedélyek hatókörét tartalmazó karakterlánc. Ha egynél több szerepkört továbbít, azokat Csípés tömbként kell átadni. |
| **exp** (nem kötelező) |Azt az időpontot jelzi, ameddig a jogkivonat lejár. Az értéket UNIX-timestampként kell átadni. |
| **NBF** (nem kötelező) |Azt az időpontot jelzi, amikor a jogkivonat érvényesnek indul. Az értéket UNIX-timestampként kell átadni. |

A minta alkalmazás-jogkivonat A következőképpen néz ki:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

A dekódoláskor a következőképpen néz ki:

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

Az SDK-k olyan metódusok érhetők el, amelyek megkönnyítik az alkalmazás-tokenek létrehozását. A .NET esetében például megtekintheti a [Microsoft. PowerBI. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) osztályt és a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metódusokat.

A .NET SDK esetében a [hatókörökre](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes)is hivatkozhat.

## <a name="scopes"></a>Hatókörök

Beágyazási jogkivonatok használatakor érdemes korlátozni az erőforrások használatát, amelyekhez hozzáférést biztosít. Ezért létrehozhat egy jogkivonatot hatókörön belüli engedélyekkel.

A Power BI munkaterület-gyűjtemények számára elérhető hatókörök a következők.

|Hatókör|Leírás|
|---|---|
|Adatkészlet. Read|Engedélyt biztosít a megadott adatkészlet olvasására.|
|Adatkészlet. Write|Engedélyt biztosít a megadott adatkészletbe való írásra.|
|Adatkészlet. ReadWrite|Engedélyt biztosít a megadott adatkészlet olvasására és írására.|
|Jelentés. olvasás|Engedélyt biztosít a megadott jelentés megtekintésére.|
|Jelentés. ReadWrite|Engedélyt biztosít a megadott jelentés megtekintésére és szerkesztésére.|
|Munkaterület. report. Create|Engedélyt ad egy új jelentés létrehozására a megadott munkaterületen belül.|
|Munkaterület. report. Copy|Engedélyt biztosít a meglévő jelentések klónozására a megadott munkaterületen belül.|

Több hatókört is megadhat, ha a hatókörök között a következőhöz hasonló térközt használ.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Szükséges jogcímek – hatókörök**

szolgáltatáskapcsolódási pont: a (z) {scopesClaim} scopesClaim lehet karakterlánc vagy karakterlánc, amely a munkaterület erőforrásaira (jelentés, adatkészlet stb.) vonatkozó engedélyek megadását is lehetővé teszi.

A megadott hatókörökkel rendelkező dekódolású jogkivonat a következőhöz hasonlóan néz ki:

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

|Művelet|Célerőforrás|Jogkivonat-engedélyek|
|---|---|---|
|Hozzon létre (memóriában) egy új jelentést egy adatkészlet alapján.|Adatkészlet|Adatkészlet. Read|
|Hozzon létre (memóriában) egy új jelentést egy adatkészlet alapján, és mentse a jelentést.|Adatkészlet|* Adatkészlet. Read<br>* Munkaterület. report. Create|
|Meglévő jelentés megtekintése és feltárása/szerkesztése (memóriában). A Report. Read azt jelenti, hogy az adatkészlet. Read. A Report. Read nem engedélyezi a módosítások mentését.|Jelentés|Jelentés. olvasás|
|Meglévő jelentés szerkesztése és mentése.|Jelentés|Jelentés. ReadWrite|
|Egy jelentés másolatának mentése (Mentés másként)|Jelentés|* Jelentés. olvasás<br>* Munkaterület. report. Copy|

## <a name="heres-how-the-flow-works"></a>A folyamat működése
1. Másolja az API-kulcsokat az alkalmazásba. A kulcsokat a **Azure Portalban**érheti el.
   
    ![Hol találhatók az API-kulcsok a Azure Portalban](media/get-started-sample/azure-portal.png)
1. A token érvényesít egy jogcímet, és lejárati idővel rendelkezik.
   
    ![Alkalmazás-jogkivonat folyamata – jogkivonat-érvényesítő jogcím](media/get-started-sample/token-2.png)
1. A token egy API-hozzáférési kulccsal lesz aláírva.
   
    ![Alkalmazás-jogkivonat flow-tokenje aláírva](media/get-started-sample/token-3.png)
1. Felhasználói kérelmek a jelentés megtekintéséhez.
   
    ![Alkalmazás-jogkivonat folyamata – felhasználói kérelmek a jelentés megtekintéséhez](media/get-started-sample/token-4.png)
1. A token API-hozzáférési kulcsokkal van érvényesítve.
   
   ![Alkalmazás-jogkivonat folyamata – a jogkivonat ellenőrzése megtörtént](media/get-started-sample/token-5.png)
1. Power BI a munkaterület-gyűjtemények jelentést küld a felhasználónak.
   
   ![Alkalmazás-jogkivonat folyamatábrája – szolgáltatás küldése a felhasználónak](media/get-started-sample/token-6.png)

Miután **Power bi munkaterület-gyűjtemények** jelentést küld a felhasználónak, a felhasználó megtekintheti a jelentést az egyéni alkalmazásban. Ha például importálta az [értékesítési adatok elemzése PBIX mintát](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), a minta webalkalmazás az alábbihoz hasonló lesz:

![Az alkalmazásban beágyazott jelentés mintája](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Lásd még:

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Ismerkedés a Microsoft Power BI Workspace-gyűjtemények példával](get-started-sample.md)  
[Gyakori Microsoft Power BI Workspace-gyűjtemények forgatókönyvei](scenarios.md)  
[Ismerkedés a Microsoft Power BI Workspace-gyűjtemények használatába](get-started.md)  
[PowerBI – CSharp git-tárház](https://github.com/Microsoft/PowerBI-CSharp)

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)
