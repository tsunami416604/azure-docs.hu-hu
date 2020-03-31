---
title: Az Azure Data Lake Analytics U-SQL feladathibáinak elhárítása a .NET Framework 4.7.2 frissítés miatt
description: A .NET Framework 4.7.2-es frissítés miatt hibaelhárításaz U-SQL feladathibákkal.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213581"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Az Azure Data Lake Analytics a .NET Framework 4.7.2-es verzióra frissít

Az Azure Data Lake Analytics alapértelmezett futásideje a .NET Framework 4.5.2-es verzióról a .NET Framework 4.7.2-es verzióra frissít. Ez a módosítás a módosítások feltörésének kis kockázatát mutatja be, ha az U-SQL-kód egyéni szerelvényeket használ, és ezek az egyéni szerelvények .NET könyvtárakat használnak.

A .NET Framework 4.5.2-ről a 4.7.2-es verzióra történő frissítés azt jelenti, hogy az U-SQL-futásidőben (az alapértelmezett futásidőben) üzembe helyezett . A .

A .NET Framework 4.7.2-re való frissítés befejezése után a rendszer felügyelt kódja 4.7.2-es verzióként fog futni, a felhasználó által biztosított könyvtárak, például az U-SQL egyéni szerelvények a szerelvény verziójának megfelelő visszamenőlegesen kompatibilis módban fognak futni. keletkezik.

- Ha a szerelvény DL-ek a 4.5.2-es verzióhoz jönlétre, az üzembe helyezett keretrendszer 4.5.2-es kódtárakként kezeli őket, így (néhány kivételtől eltekintve) 4.5.2-es szemantikát biztosít.
- Most már használhatja az U-SQL egyéni szerelvényeket, amelyek a 4.7.2-es verziójú szolgáltatásokat használják, ha a .

A . Javasoljuk, hogy az alábbi eljárással ellenőrizze a visszamenőleges kompatibilitási problémákat.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Hogyan lehet ellenőrizni a visszamenőleges kompatibilitási problémákat

Ellenőrizze, hogy lehetséges-e a visszamenőleges kompatibilitási problémák, ha az U-SQL egyéni szerelvényeiben futtatja a .NET kompatibilitási ellenőrzéseket a .NET-kódon.

> [!Note]
> Az eszköz nem észleli a tényleges törési módosításokat. csak azokat a .NET API-kat azonosítja, amelyek (bizonyos bemenetek esetében) problémákat okozhatnak. Ha értesítést kap egy problémáról, a kód továbbra is rendben lehet, de további részleteket kell bemegadnia.

1. Futtassa a visszafelé kompatibilitás-ellenőrzőt a .NET DLL-eken
   1. A Visual Studio bővítmény használata a [.NET Portability Analyzer Visual Studio extension-en](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Az önálló eszköz letöltése és használata a [GitHub dotnetapiportból.](https://github.com/microsoft/dotnet-apiport) Az önálló eszköz futtatására vonatkozó utasítások a [GitHub dotnetapiport ban találhatók, amely megszakítja a módosításokat](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. A 4.7.2. kompatibilitást, `read isRetargeting == True` azonosítja a lehetséges problémákat.
2. Ha az eszköz jelzi, hogy a kódot befolyásolhatja-e a lehetséges visszamenőleges összeférhetetlenség (az inkompatibilitás néhány gyakori példáját alább felsorolta), akkor további
   1. A kód elemzése és annak azonosítása, hogy a kód értékeket ad-e át az érintett API-knak
   1. Futásidejű ellenőrzés végrehajtása. A futásidejű üzembe helyezés nem történik egymás mellett az ADLA-ban. A frissítés előtt futásidejű ellenőrzést hajthat végre a VisualStudio helyi futtatásával egy helyi .
3. Ha valóban hatással van önre a visszafelé inkompatibilitás, tegye meg a szükséges lépéseket a javításhoz (például az adatok vagy a kódlogika javítása).

A legtöbb esetben nem befolyásolhatja a visszamenőleges összeférhetetlenség.

## <a name="timeline"></a>Idővonal

Itt ellenőrizheti az új futásidejű [hibaidők](runtime-troubleshoot.md)telepítését, és megnézheti a korábbi sikeres feladatokat.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Mi a teendő, ha nem tudom időben átvizsgálni a kódomat?

A feladatot elküldheti a régi futásidejű verzióellen (amely a 4.5.2-es verziót célozza, azonban a . A viselkedés miatt továbbra is előfordulhat, hogy a visszamenőleges kompatibilitási problémák némelyike megtörténik.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Melyek a leggyakoribb visszafelé kompatibilitási problémák, amelyekkel találkozhat

A leggyakoribb visszafelé inkompatibilitás, hogy az ellenőrző valószínűleg azonosítani (mi generált ez a lista futtatásával az ellenőrző saját belső ADLA-feladatok), amely könyvtárak érintik (megjegyzés: hogy lehet hívni a könyvtárak csak közvetve, ezért fontos, hogy tegye meg a szükséges lépéseket, #1, hogy ellenőrizze, ha a munkahelyek érinti), és a lehetséges intézkedések orvoslására. Megjegyzés: Szinte minden esetben a saját munkahelyünkön, a figyelmeztetések kiderült, hogy hamis pozitív miatt szűk jellegét a legtöbb törés változásokat.

- Az IAsyncResult.CompletedSzinkron tulajdonságnak helyesnek kell lennie az eredményül kapott feladat befejezéséhez
  - A TaskFactory.FromAsync hívásakor az IAsyncResult.CompletedSynchronously tulajdonság megvalósításának helyesnek kell lennie az eredményül kapott feladat befejezéséhez. Ez azt, hogy a tulajdonságnak igaz értéket kell visszaadnia, ha a megvalósítás szinkron módon fejeződött be. Korábban a tulajdonság nem lett ellenőrizve.
  - Érintett könyvtárak: mscorlib, System.Threading.Tasks
  - Javasolt művelet: Győződjön meg arról, hogy a TaskFactory.FromAsync helyesen ad vissza.

- A DataObject.GetData mostantól UTF-8 néven olvassa be az adatokat
  - A .NET Framework 4 rendszert megcélzó vagy a .NET Framework 4.5.1-es vagy korábbi verziókon futó alkalmazások esetében a DataObject.GetData a HTML formátumú adatokat ASCII-karakterláncként olvassa be. Ennek eredményeképpen a nem ASCII karaktereket (amelyek ASCII-kódjai nagyobbak 0x7F-nél) két véletlenszerű karakter képviseli.#N##N#A .NET Framework 4.5-ös `DataObject.GetData` vagy újabb rendszert megcélzó és a .NET Framework 4.5.2 rendszeren futó alkalmazások esetében a HTML-formátumú adatokat UTF-8 formátumúként olvassa be, ami a 0x7F-nél nagyobb karaktereket jelöli.
  - Érintett könyvtárak: Glo
  - Javasolt művelet: Győződjön meg arról, hogy a beolvasott adatok a kívánt formátumúak

- Az XmlWriter érvénytelen helyettesítő párokat dob
  - A .NET Framework 4.5.2-es vagy korábbi verzióit célzó alkalmazások esetében a kivételtartalék kezelés használatával érvénytelen helyettesítő pár írása nem mindig jelent kivételt. A . `ArgumentException`
  - Érintett tárak: System.Xml, System.Xml.ReaderWriter
  - Javasolt művelet: Győződjön meg arról, hogy nem érvénytelen helyettesítő párt ír, amely argumentumkivételt okoz

- A HtmlTextWriter `<br/>` nem jeleníti meg helyesen az elemet
  - A . `HtmlTextWriter.RenderBeginTag()` `HtmlTextWriter.RenderEndTag()` `<BR />` `<BR />`
  - Érintett tárak: System.Web
  - Javasolt művelet: Győződjön meg `<BR />` arról, hogy beszúrja a várhatóan várt mennyiséget, hogy ne jelenjen meg véletlenszerű viselkedés az éles környezetben

- A CreateDefaultAuthorizationContext hívása null argumentummal megváltozott
  - A null authorizationPolicies argumentumhoz intézett `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` hívás által visszaadott AuthorizationContext megvalósítása megváltoztatta annak megvalósítását a .
  - Érintett könyvtárak: System.IdentityModel
  - Javasolt művelet: Győződjön meg arról, hogy az új várt viselkedést kezeli, ha null engedélyezési házirend van.
  
- Az RSACng mostmár helyesen tölti be a nem szabványos kulcsméretű RSA-kulcsokat
  - A . `GetRSAPublicKey()` `GetRSAPrivateKey()` A `CryptographicException` a "A kért kulcs mérete nem támogatott" üzenet jelenik meg. A . Hasonlóképpen, `RSA.ImportParameters()` `RSACng.ImportParameters()` és most a munka nem szabványos `CryptographicException`kulcs méretben dobás nélkül 's.
  - Érintett könyvtárak: mscorlib, System.Core
  - Javasolt művelet: Győződjön meg arról, hogy az RSA-kulcsok a várt módon működnek

- Az elérési út kettőspont-ellenőrzései szigorúbbak
  - A . A megfelelő meghajtóelválasztó (kettőspont) szintaxisának ellenőrzése helyesebbé vált, aminek az volt a mellékhatása, hogy néhány URI-útvonalat blokkolt néhány olyan Elérési út API-jában, ahol korábban tolerálták őket.
  - Érintett könyvtárak: mscorlib, System.Runtime.Extensions
  - Javasolt művelet:

- ClaimsIdentity konstruktorok hívásai
  - A . `T:System.Security.Claims.ClaimsIdentity` `T:System.Security.Principal.IIdentity` `P:System.Security.Claims.ClaimsIdentify.Actor` Ha `T:System.Security.Principal.IIdentity` az argumentum `T:System.Security.Claims.ClaimsIdentity` objektum, `P:System.Security.Claims.ClaimsIdentify.Actor` és `T:System.Security.Claims.ClaimsIdentity` az objektum `null`tulajdonsága nem, akkor a `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonság a `M:System.Security.Claims.ClaimsIdentity.Clone` metódussal lesz csatolva. A Framework 4.6.1-es és `P:System.Security.Claims.ClaimsIdentify.Actor` korábbi verzióiban a tulajdonság meglévő hivatkozásként van csatolva. A változás miatt a . `P:System.Security.Claims.ClaimsIdentify.Actor` `T:System.Security.Claims.ClaimsIdentity` `P:System.Security.Claims.ClaimsIdentify.Actor` `T:System.Security.Principal.IIdentity` A .
  - Érintett könyvtárak: mscorlib
  - Javasolt művelet: Győződjön meg arról, hogy a jogcímidentitás a várt módon működik az új futásidőben

- A vezérlőkarakterek szerializálása datacontractjsonininizer-rel mostmár kompatibilis az ECMAScript V6 és V8 rendszerrel
  - A . A .
  - Érintett könyvtárak: System.Runtime.Serialization.Json
  - Javasolt művelet: A DataContractJsonSerializer azonos viselkedésének biztosítása
