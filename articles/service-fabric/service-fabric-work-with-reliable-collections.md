---
title: A Reliable Collections használata
description: Ismerje meg az Azure Service Fabric-alkalmazások megbízható gyűjteményeivel való együttműködés ajánlott eljárásokat.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94836a37a62e3eeffb94d891980cc02694bd973e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409812"
---
# <a name="working-with-reliable-collections"></a>A Reliable Collections használata
A Service Fabric egy állapotalapú programozási modellt kínál a .NET fejlesztők számára a Reliable Collections rendszeren keresztül. Pontosabban a Service Fabric megbízható szótárat és megbízható várólista-osztályokat biztosít. Ha ezeket az osztályokat használja, az állapot particionálva lesz (a méretezhetőség érdekében), replikálódik (a rendelkezésre állás érdekében), és egy partíción belül (ACID szemantika) tranzakciós. Nézzük meg egy megbízható szótárobjektum tipikus használatát, és nézzük meg, hogy valójában mit csinál.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

A megbízható szótárobjektumokon végzett összes művelethez (kivéve a nem visszavonható ClearAsync műveletet), iTransaction objektumot kell igényelni. Ez az objektum minden olyan módosítást társított hozzá, amelyet egyetlen partíción belül bármely megbízható szótáron és/vagy megbízható várólista-objektumon próbál végrehajtani. ITransaction objektumot úgy szerezhet be, hogy meghívja a partíció StateManager CreateTransaction metódusát.

A fenti kódban az ITransaction objektum átkerül egy megbízható szótár AddAsync metódusába. Belsőleg, szótár módszerek, amelyek elfogadják a kulcsot, hogy egy olvasó / író zár társított kulcs. Ha a metódus módosítja a kulcs értékét, a metódus írási zárolást vesz fel a kulcsra, és ha a metódus csak a kulcs értékéből olvas, akkor olvasási zárolást vesz nek a kulcson. Mivel az AddAsync módosítja a kulcs értékét az új, átadott értékre, a kulcs írási zárolása kerül sor. Így ha 2 (vagy több) szál egyszerre próbál értékeket hozzáadni ugyanazzal a kulccsal, az egyik szál megszerzi az írási zárolást, a többi szál pedig blokkolja. Alapértelmezés szerint a metódusok legfeljebb 4 másodpercig blokkolnak a zárolás megszerzéséhez; 4 másodperc elteltével a metódusok timeoutexception-et dobnak. Metódus túlterhelések léteznek, amely lehetővé teszi, hogy adja át egy explicit időtúllépési érték, ha szeretné.

Általában úgy írja meg a kódot, hogy reagáljon a TimeoutException-re, és a teljes műveletet újra megpróbálja (ahogy az a fenti kódban látható). Az én egyszerű kódot, én csak hívja Task.Delay halad 100 ezredmásodperc minden alkalommal. De a valóságban, lehet, hogy jobban jársz, ha valami exponenciális visszalépési késleltetést használsz helyette.

A zárolás beszerzése után az AddAsync hozzáadja a kulcs- és értékobjektum-hivatkozásokat az ITransaction objektumhoz társított belső ideiglenes szótárhoz. Ez azért történik, hogy az Ön számára a read-your-own-writes szemantika. Ez azt jelenti, hogy az AddAsync hívása után a TryGetValueAsync (ugyanazt az ITransaction objektumot használó) későbbi hívása akkor is visszaadja az értéket, ha még nem véglegesítte a tranzakciót. Ezután az AddAsync szerializálja a kulcsot és az értékobjektumokat a bájttömbökbe, és ezeket a bájttömböket hozzáfűzi a helyi csomópont naplófájljához. Végül addAsync elküldi a bájttömbök et az összes másodlagos replikák, így ugyanazokkal a kulcs/érték információk. Annak ellenére, hogy a kulcs/érték információ naplófájlba lett írva, az információ nem tekinthető a szótár részének, amíg a hozzájuk társított tranzakció véglegesítése meg nem történt.

A fenti kódban a CommitAsync hívásvéglegesíti a tranzakció összes műveletét. Pontosabban a helyi csomóponton lévő naplófájlhoz fűz idvanál, és elküldi a véglegesítési rekordot az összes másodlagos replikának. Miután a kópiák kvóruma (többsége) válaszolt, minden adatmódosítás állandónak minősül, és az ITransaction objektumon keresztül manipulált kulcsokhoz társított zárolások felszabadulnak, így más szálak/tranzakciók is kezelhetik ugyanazokat a kulcsokat és azok értékeit.

Ha a CommitAsync nem kerül megnevezésre (általában egy kivétel miatt), akkor az ITransaction objektum ot megsemmisíti. Egy nem véglegesített ITransaction-objektum ártalmatlanításakor a Service Fabric hozzáfűzi a megszakítási adatokat a helyi csomópont naplófájljához, és semmit sem kell küldeni a másodlagos replikáknak. És akkor, minden zár kapcsolódó kulcsokat, amelyek manipulálták a tranzakció nkeresztül szabadulnak fel.

## <a name="volatile-reliable-collections"></a>Illékony megbízható gyűjtemények 
Egyes számítási feladatokban, például egy replikált gyorsítótárban például, az alkalmi adatvesztés is elviselhető. A lemezre történő adatmegőrzés elkerülése jobb késéseket és átviteli réseket tesz lehetővé a Megbízható szótárak írásakor. A perzisztencia hiányának kompromisszuma az, hogy ha a kvórum vesztesége bekövetkezik, teljes adatvesztés következik be. Mivel a kvórum elvesztése ritka jelenség, a megnövekedett teljesítmény érdemes lehet a ritka lehetőségét adatvesztés az ilyen számítási feladatok.

Jelenleg az illékony támogatás csak megbízható szótárak és megbízható várólisták, és nem ReliableConcurrentQueues érhető el. Kérjük, olvassa el a [kifogások](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) listáját, hogy tájékoztassa a döntést arról, hogy használja illékony gyűjtemények.

Ha engedélyezni szeretné az illékony ```HasPersistedState``` támogatást a szolgáltatásban, állítsa a jelzőt a szolgáltatástípus-deklarációban a beállításra, ```false```így:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>A meglévő megőrzött szolgáltatások nem tehetők volatilissé, és fordítva. Ha ezt szeretné, törölnie kell a meglévő szolgáltatást, majd telepítenie kell a szolgáltatást a frissített jelzővel. Ez azt jelenti, hogy hajlandónak kell lennie arra, ```HasPersistedState``` hogy teljes adatvesztést okozzon, ha meg szeretné változtatni a jelzőt. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Gyakori buktatókat, és hogyan lehet elkerülni őket
Most, hogy megértette, hogyan működnek a megbízható gyűjtemények belsőleg, vessünk egy pillantást néhány gyakori visszaélések őket. Lásd az alábbi kódot:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ha normál .NET szótárral dolgozik, hozzáadhat egy kulcsot/értéket a szótárhoz, majd módosíthatja egy tulajdonság (például LastLogin) értékét. Ez a kód azonban nem fog megfelelően működni egy megbízható szótárral. Ne feledje, a korábbi vita, a hívás AddAsync szerializálja a kulcs/érték objektumok byte tömbök, majd menti a tömböket egy helyi fájlba, és elküldi őket a másodlagos replikák. Ha később módosítja a tulajdonságot, az csak a memóriában lévő tulajdonság értékét módosítja; nem érinti a helyi fájlt vagy a replikáknak küldött adatokat. Ha a folyamat összeomlik, mi van a memóriában eldobják. Amikor egy új folyamat elindul, vagy ha egy másik replika lesz az elsődleges, majd a régi tulajdonság értéke áll rendelkezésre.

Nem tudom eléggé hangsúlyozni, hogy milyen könnyű, hogy az a fajta hiba fent látható. És, akkor csak tanulni a hibát, ha / amikor a folyamat lemegy. A kód helyes írása egyszerűen a két sor megfordítása:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Íme egy másik példa, amely egy gyakori hibát mutat be:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

A hagyományos .NET szótáraknál a fenti kód is jól működik, és gyakori minta: a fejlesztő egy kulcsot használ egy érték felkereséséhez. Ha az érték létezik, a fejlesztő módosítja a tulajdonság értékét. Azonban a megbízható gyűjtemények, ez a kód ugyanazt a problémát mutatja, mint már tárgyalt: **nem szabad módosítani egy objektumot, ha már adott, hogy egy megbízható gyűjtemény.**

Egy megbízható gyűjtemény értékének frissítésének helyes módja, ha hivatkozást kap a meglévő értékre, és nem módosíthatónak tekinti az e hivatkozás által említett objektumot. Ezután hozzon létre egy új objektumot, amely az eredeti objektum pontos másolata. Most módosíthatja az új objektum állapotát, és beírhatja az új objektumot a gyűjteménybe, hogy szerializálva legyen a bájttömbökbe, hozzáfűzve a helyi fájlhoz, és elküldve a replikákhoz. A módosítás(ok) véglegesítése után a memóriában lévő objektumok, a helyi fájl és az összes kópia pontosan azonos állapotú. Minden jó!

Az alábbi kód bemutatja, hogyan lehet egy értéket megbízható gyűjteményben frissíteni:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Nem módosítható adattípusok definiálása a programozói hibák megelőzése érdekében
Ideális esetben azt szeretnénk, ha a fordító hibákat jelentene, ha véletlenül olyan kódot hoz létre, amely mutálódik egy objektum állapotáról, amelyet nem módosíthatónak kell tekintenie. De a C# fordító nem képes erre. Ezért a potenciális programozói hibák elkerülése érdekében javasoljuk, hogy a megbízható gyűjteményekkel használt típusokat állandó típusként határozza meg. Ez konkrétan azt jelenti, hogy ragaszkodik az alapvető értéktípusokhoz (például számokhoz [Int32, UInt64 stb.], DateTime, Guid, TimeSpan és hasonlók). A Karakterlánc ot is használhatja. A legjobb, ha elkerüli a gyűjtemény tulajdonságait, mivel szerializálása és deszerializálása gyakran árt a teljesítménynek. Ha azonban gyűjteménytulajdonságokat szeretne használni, javasoljuk a használatát. NET nem módosítható gyűjtemények könyvtára ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ez a tár letölthető https://nuget.orga alkalmazásból. Azt is javasoljuk, hogy zárja le az osztályokat, és tegye a mezőket csak olvashatóvá, amikor csak lehetséges.

Az alábbi UserInfo típus bemutatja, hogyan definiálható egy megváltoztathatatlan típus a fent említett javaslatok kihasználásával.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Az ItemId típus szintén megváltoztathatatlan típus, ahogy az itt látható:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Sémaverziók (frissítések)
Belsőleg, Megbízható gyűjtemények szerializálja az objektumok at. NET DataContractSerializer. A szerializált objektumok megmaradnak az elsődleges replika helyi lemezén, és a másodlagos replikákra is továbbítódnak. A szolgáltatás érlelődésével valószínűleg módosítani szeretné a szolgáltatás által igényelt adatok (séma) típusát. Nagy gonddal közelítse meg az adatok verziószámozását. Mindenekelőtt mindig képesnek kell lennie a régi adatok deszerializálására. Ez konkrétan azt jelenti, hogy a deszerializációs kódnak végtelenül visszamenőlegesen kompatibilisnek kell lennie: A szolgáltatáskód 333-as verziójának képesnek kell lennie a szolgáltatáskód 1.-es verziójának 5 évvel ezelőtti megbízható gyűjteményében elhelyezett adatokon való működésre.

Továbbá a szolgáltatáskód egyszerre egy frissítési tartománnyal frissül. Így a frissítés során a szolgáltatáskód két különböző verziója fut egyszerre. El kell kerülni, hogy a szolgáltatáskód új verziója használja az új sémát, mivel előfordulhat, hogy a szolgáltatáskód régi verziói nem tudják kezelni az új sémát. Ha lehetséges, a szolgáltatás minden egyes verzióját úgy kell megterveznie, hogy egy verzióval kompatibilis legyen. Ez azt jelenti, hogy a szolgáltatáskód V1-jének képesnek kell lennie arra, hogy figyelmen kívül hagyja a nem kifejezetten kezelt sémaelemeket. Azonban képesnek kell lennie arra, hogy mentse az adatokat, hogy nem kifejezetten tud, és írja vissza, amikor egy szótár kulcs vagy érték frissítése.

> [!WARNING]
> Bár módosíthatja a sémát egy kulcs, meg kell győződnie arról, hogy a kulcs kivonatoló kódja és egyenlő algoritmusok stabilak. Ha módosítja ezen algoritmusok működését, soha többé nem fogja tudni megkeresni a kulcsot a megbízható szótárban.
> A .NET karakterláncok kulcsként használhatók, de magát a karakterláncot használják kulcsként – ne használja kulcsként a String.GetHashCode eredményét.

Azt is megteheti, amit általában két frissítésnek neveznek. A kétfázisú frissítés, a szolgáltatás frissítése v1-ről V2: V2 tartalmazza a kódot, amely tudja, hogyan kell kezelni az új séma változás, de ez a kód nem hajtható végre. Amikor a V2-kód v1-es adatokat olvas be, akkor működik rajta, és v1-es adatokat ír. Ezután a frissítés befejezése után az összes frissítési tartományok, valahogy jelezheti a futó V2-példányok, hogy a frissítés befejeződött. (Ennek egyik jelzése a konfigurációs frissítés bevezetése; ez teszi ezt kétfázisú frissítéssé.) Most a V2-példányok olvashatják a V1-adatokat, v2-es adatokká alakíthatják, működtethetik, és V2-adatként írhatják ki. Amikor más példányok v2-adatokat olvasnak, nem kell konvertálniuk, csak működnek rajta, és kiírják a V2-adatokat.

## <a name="next-steps"></a>További lépések
A kompatibilis adategyezmények továbbítása című témakörben [olvashat.](https://msdn.microsoft.com/library/ms731083.aspx)

Az adatszerződések verziószámozásával kapcsolatos gyakorlati tanácsokról az [Adatszerződés-verziószámozás című](https://msdn.microsoft.com/library/ms731138.aspx) témakörben olvashat.

A verziótoleráns adategyezmények megvalósításáról a [Verziótűrő szerializálási visszahívások című témakörben olvashat.](https://msdn.microsoft.com/library/ms733734.aspx)

Ha meg szeretné tudni, hogyan hozhat létre olyan adatstruktúrát, amely több verzióban is együttműködhet, olvassa el az [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)
