---
title: A Azure Data Lake Analytics U-SQL-feladatok hibáinak elhárítása a .NET-keretrendszer 4.7.2 frissítése miatt
description: A U-SQL-feladatok hibáinak elhárítása a .NET-keretrendszer 4.7.2 való frissítés miatt.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213581"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>A Azure Data Lake Analytics a .NET-keretrendszer v 4.7.2 frissít

Az alapértelmezett futtatókörnyezet Azure Data Lake Analytics a .NET-keretrendszer v 4.5.2-ről a .NET-keretrendszer v-4.7.2 való frissítésre. Ez a változás kisebb kockázatot jelent, ha az U-SQL-kód egyéni szerelvényeket használ, és ezek az egyéni szerelvények a .NET-kódtárakat használják.

A .NET-keretrendszer 4.5.2-es verziójáról a 4.7.2 verzióra történő frissítés azt jelenti, hogy az U-SQL-futtatókörnyezetben (az alapértelmezett futtatókörnyezetben) üzembe helyezett .NET-keretrendszer mostantól mindig 4.7.2 lesz. Nincs párhuzamos lehetőség a .NET-keretrendszer verzióihoz.

A .NET-keretrendszer 4.7.2 való frissítés befejezése után a rendszer felügyelt kódja 4.7.2 verzióként fut, a felhasználó által megadott kódtárak, például a U-SQL-szerelvények a szerelvényhez tartozó verzióhoz megfelelő visszafelé kompatibilis módban futnak. létrehozva:.

- Ha a szerelvény dll-jei az 4.5.2-es verzióhoz jönnek létre, a központilag telepített keretrendszer 4.5.2 függvénytárként fog megjelenni, amely (néhány kivétellel) 4.5.2-es szemantikai információkkal szolgál.
- Mostantól olyan U-SQL-alapú egyéni szerelvényeket is használhat, amelyek a 4.7.2-funkciók használatát teszik elérhetővé, ha a .NET-keretrendszer 4.7.2 célozza meg.

A .NET-keretrendszer 4.7.2 való frissítés miatt előfordulhat, hogy a .NET-alapú egyéni szerelvényeket használó U-SQL-feladatokban felmerülő változtatásokat is be kell állítani. Javasoljuk, hogy a visszamenőleges kompatibilitási problémákat az alábbi eljárással keresse meg.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>A visszamenőleges kompatibilitási problémák ellenőrzése

Ellenőrizze, hogy lehetséges-e a visszamenőleges kompatibilitást okozó hibák elhárítása. Ehhez futtassa a .NET-kompatibilitási ellenőrzéseket a .NET-kódban a U-SQL egyéni szerelvényekben.

> [!Note]
> Az eszköz nem ismeri fel a tényleges törési változásokat. Ez csak az úgynevezett .NET API-k azonosítására szolgál, amelyek (bizonyos bemenetek esetében) problémákat okozhatnak. Ha értesítést kap a hibáról, akkor előfordulhat, hogy a kód továbbra is rendben van, azonban további részleteket is érdemes megadnia.

1. Futtassa a visszamenőleges kompatibilitás-ellenőrzési szolgáltatást a .NET-DLL-eken vagy a
   1. A Visual Studio bővítmény használata a [.net-hordozhatósági elemző Visual Studio-bővítményben](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Az önálló eszköz letöltése és használata a [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Az önálló eszköz futtatására vonatkozó utasítások a [GitHub dotnetapiport-megszakítási változások](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 4\.7.2. kompatibilitási `read isRetargeting == True` azonosítja a lehetséges problémákat.
2. Ha az eszköz azt jelzi, hogy a kód érintheti-e a lehetséges visszamenőleges inkompatibilitást (néhány gyakori példa az inkompatibilitásra), további ellenőrzéshez használhatja a következőt:
   1. A kód elemzése és annak azonosítása, hogy a kód az érintett API-kra mutat-e értékeket
   1. Végezzen futtatókörnyezet-ellenőrzéseket. A futtatókörnyezet központi telepítése nem párhuzamosan történik a ADLA-ben. A frissítés előtt elvégezheti a futtatókörnyezet ellenőrzését, ha a VisualStudio helyi futtatását helyi .NET-keretrendszerrel 4.7.2 egy jellemző adatkészleten keresztül.
3. Ha valóban negatív hatással van egy visszafelé inkompatibilitásra, végezze el a szükséges lépéseket a kijavításához (például az adatai vagy a kód logikájának javításához).

A legtöbb esetben nem érinti a visszafelé való inkompatibilitást.

## <a name="timeline"></a>Idővonal

Itt megtekintheti [a futásidejű új](runtime-troubleshoot.md)futtatókörnyezetet, valamint a korábbi sikeres feladatok megtekintését.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Mi a teendő, ha nem tudom beolvasni a kódot az időben

Elküldheti a feladatot a régi futtatókörnyezet-verzióra (amely az 4.5.2-es célzásra épül), azonban a .NET-keretrendszer egymás melletti képességeinek hiánya miatt még csak 4.5.2 kompatibilitási módban fog futni. Ennek a viselkedésnek a miatt továbbra is előfordulhatnak visszamenőleges kompatibilitási problémák.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Melyek a leggyakoribb visszamenőleges kompatibilitási problémák, amelyekkel találkozhat

A leggyakoribb visszamenőleges inkompatibilitás, amelyet az ellenőrzés valószínűleg azonosít (ezt a listát a saját belső ADLA-feladatain futtatjuk), mely könyvtárak érintettek (Megjegyzés: a kódtárak csak közvetett módon hívhatók meg, ezért fontos a szükséges művelet végrehajtása #1 a feladatok érintettvé tételének ellenőrzéséhez) és a lehetséges műveletek orvoslásához. Megjegyzés: a saját feladatokhoz szinte minden esetben a figyelmeztetések hamis pozitívnak bizonyultak a legtöbb feltörési változás miatt.

- A IAsyncResult. CompletedSynchronously tulajdonságnak helyesnek kell lennie az eredményül kapott tevékenység befejezéséhez.
  - A TaskFactory. FromAsync hívásakor a IAsyncResult. CompletedSynchronously tulajdonság megvalósításának helyesnek kell lennie az eredményül kapott tevékenység befejezéséhez. Vagyis a tulajdonságnak igaz értéket kell visszaadnia, és csak akkor, ha a megvalósítás szinkron módon fejeződött be. Korábban a tulajdonság nincs bejelölve.
  - Érintett kódtárak: mscorlib, System. Threading. Tasks
  - Javasolt művelet: Ellenőrizze, hogy a TaskFactory. FromAsync igaz értéket ad-e vissza.

- A DataObject. GetData mostantól UTF-8-ként kérdezi le az adatfájlokat
  - A .NET-keretrendszer 4-es verzióját vagy a .NET-keretrendszer 4.5.1-es vagy korábbi verzióit célzó alkalmazások esetében a DataObject. GetData egy ASCII-karakterláncként kéri le a HTML formátumú adatformátumot. Ennek eredményeképpen a nem ASCII-karakterek (a 0x7F nagyobb méretű ASCII-kódok) két véletlenszerű karakterből állnak. #N # #N # a .NET-keretrendszer 4,5-es vagy újabb verzióit célzó alkalmazásokhoz, valamint a .NET-keretrendszer 4.5.2-es verziójához, `DataObject.GetData` lekéri a HTML formátumú, UTF-8 karaktert, amely a 0x7F nagyobb karaktereket jelöl.
  - Érintett kódtárak: Glo
  - Javasolt művelet: a beolvasott adatformátumot a kívánt formátumban kell megadni

- A XmlWriter érvénytelen helyettesítő párokat dob
  - A .NET-keretrendszer 4.5.2-es vagy korábbi verzióit célzó alkalmazások esetén érvénytelen helyettesítő párokat kell megírni, ha a kivétel-visszaállítási szolgáltatás nem mindig kivételt jelez. A .NET-keretrendszer 4,6-at célzó alkalmazások esetében az érvénytelen helyettesítő párok írására tett kísérlet egy `ArgumentException`.
  - Érintett kódtárak: System. XML, System. xml. ReaderWriter
  - Javasolt művelet: Ügyeljen arra, hogy ne írjon be érvénytelen helyettesítő párt, amely argumentum-kivételt eredményez.

- A HtmlTextWriter nem jeleníti meg helyesen `<br/>` elemet
  - A .NET-keretrendszer 4,6-es verziójától kezdve a `HtmlTextWriter.RenderBeginTag()` és `HtmlTextWriter.RenderEndTag()` meghívása egy `<BR />` elemmel pontosan csak egy `<BR />` fog beszúrni (kettő helyett)
  - Érintett kódtárak: System. Web
  - Javasolt művelet: Ügyeljen arra, hogy beillessze a várt `<BR />` mennyiségét, így az éles környezetben nem látható véletlenszerű viselkedés.

- A CreateDefaultAuthorizationContext hívása null értékű argumentummal megváltozott
  - A null értékű Applyallocationpolicy rendelkező `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` hívásával visszaadott AuthorizationContext megvalósítása megváltoztatta a .NET-keretrendszer 4,6-es verziójának megvalósítását.
  - Érintett kódtárak: System. IdentityModel
  - Javasolt művelet: gondoskodjon arról, hogy az új várt viselkedést kezelje, ha van null engedélyezési házirend
  
- A RSACng mostantól helyesen tölti be a nem szabványos kulcsú RSA-kulcsokat
  - A .NET-keretrendszer 4.6.2 előtti verzióiban az RSA-tanúsítványok nem szabványos méretekkel rendelkező ügyfelei nem férhetnek hozzá ezekhez a kulcsokhoz a `GetRSAPublicKey()` és az `GetRSAPrivateKey()` Extension metódusok segítségével. Az "a kért kulcs mérete nem támogatott" üzenettel rendelkező `CryptographicException` dobni. A .NET-keretrendszer 4.6.2 Ez a probléma kijavítva. Hasonlóképpen, a `RSA.ImportParameters()` és a `RSACng.ImportParameters()` már nem szabványos méretű kulcsokkal is működik, és nem kell eldobnia `CryptographicException`.
  - Érintett kódtárak: mscorlib, System. Core
  - Javasolt művelet: Ellenőrizze, hogy az RSA-kulcsok a várt módon működnek-e

- Az elérésiút-kettőspont ellenőrzése szigorúbb
  - A .NET-keretrendszer 4.6.2 számos módosítás történt a korábban nem támogatott elérési utak (hossz és formátum) támogatásához. A megfelelő meghajtó-elválasztó (kettőspont) szintaxisának ellenőrzése pontosabban megtörtént, aminek hatására az egyes URI-útvonalak blokkolása néhány kiválasztott elérési út API-n, ahol a szolgáltatás a tolerált volt.
  - Érintett kódtárak: mscorlib, System. Runtime. Extensions
  - Javasolt művelet:

- ClaimsIdentity konstruktorok hívásai
  - A .NET-keretrendszer 4.6.2 kezdve módosult a `T:System.Security.Claims.ClaimsIdentity` konstruktorok `T:System.Security.Principal.IIdentity` paraméterrel való beállítása a `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonság beállításával. Ha a `T:System.Security.Principal.IIdentity` argumentum egy `T:System.Security.Claims.ClaimsIdentity` objektum, és az `T:System.Security.Claims.ClaimsIdentity` objektum `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonsága nem `null`, a `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonságot a `M:System.Security.Claims.ClaimsIdentity.Clone` metódussal csatolja a rendszer. A keretrendszer 4.6.1-es és korábbi verzióiban a `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonság meglévő hivatkozásként van csatolva. A módosítás miatt a .NET-keretrendszer 4.6.2 kezdve az új `T:System.Security.Claims.ClaimsIdentity` objektum `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonsága nem egyenlő a konstruktor `T:System.Security.Principal.IIdentity` argumentumának `P:System.Security.Claims.ClaimsIdentify.Actor` tulajdonságával. A .NET-keretrendszer 4.6.1-es és korábbi verzióiban ez egyenlő.
  - Érintett kódtárak: mscorlib
  - Javasolt művelet: gondoskodjon arról, hogy a ClaimsIdentity a várt módon működjön az új futtatókörnyezetben

- A vezérlési karakterek DataContractJsonSerializer-vel való szerializálása mostantól kompatibilis a ECMAScript v6-val és a V8-val
  - A .NET-keretrendszer 4.6.2 és korábbi verzióiban a DataContractJsonSerializer nem hajtottak végre olyan speciális vezérlő karaktereket, mint például a \b, a \f és a \t, így az a ECMAScript V6-os és a V8-as szabványokkal kompatibilis. A .NET-keretrendszer 4,7-es verziótól kezdődően a vezérlő karakterek szerializálása kompatibilis a ECMAScript v6 és a V8 használatával.
  - Érintett kódtárak: System. Runtime. szerializálás. JSON
  - Javasolt művelet: azonos viselkedés biztosítása a DataContractJsonSerializer
