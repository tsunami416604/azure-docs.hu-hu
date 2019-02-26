---
title: Reliable Collections használata |} A Microsoft Docs
description: A Reliable Collections használata ajánlott eljárások megismerése.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2019
ms.author: aljo
ms.openlocfilehash: 38aef6e5ba65f67a1dd30ba2c18e180cd92624c6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805307"
---
# <a name="working-with-reliable-collections"></a>A Reliable Collections használata
A Service Fabric a .NET-fejlesztők számára a Reliable Collections-n keresztül elérhető állapot-nyilvántartó programozási modellt biztosít. Pontosabban a Service Fabric megbízható szótárban és megbízható várólista osztályokat biztosít. Ha használja ezeket az osztályokat, az állapot van particionálva (a méretezhetőség érdekében), replikálni (a rendelkezésre állás érdekében), és (az ACID szemantika) egy partíción belül. Most egy megbízható szótárban objektum egy tipikus használati tekintse meg, és tekintse meg az aktuális ténylegesen műveletet.

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

A megbízható szótárban objektumok (kivéve a ClearAsync, amely nem vonható vissza), minden művelethez szükség van a ITransaction objektum. Ez az objektum van társítva bármely és, hogy minden olyan megbízható szótárban és/vagy megbízható végrehajtani kívánt összes módosítást a várólistára ugyanazon a partíción belüli objektumok. Egy ITransaction szerez be a partíció meghívásával objektum a StateManager a CreateTransaction metódus.

A fenti kódrészletben a ITransaction objektum egy megbízható szótárban AddAsync metódusnak átadott van. Belsőleg fogadja el a kulcs szótár módszerek kulcshoz olvasó/zárolást igénybe vehet. Ha a metódus a kulcs értékét módosítja, a metódushoz zárolhatja a kulcs, és ha a metódus csak olvas a kulcs értékét, majd egy olvasási zárolás készül a kulcsot. Mivel AddAsync a kulcs értékét az új, az átadott értékre módosítja, a kulcs írási zárolása használatban van. Tehát 2 (vagy több) szálak megpróbál értékek hozzáadásához ugyanazzal a kulccsal egyszerre, ha egy hozzászóláslánc fogják beszerezni az írási zárolása, és letiltja a más szálak. Alapértelmezés szerint módszerek zárolást; akár 4 másodpercig letiltása 4 másodperc elteltével a módszerek throw egy TimeoutException. Túlterheléssel módszer létezik, így átadandó egy explicit időtúllépési értéket, ha inkább.

Általában a reagálni a TimeoutException elfogja-e, és a teljes művelet újrapróbálása (ahogyan az a fenti kód látható) kód írása. Egyszerű kódomat Task.Delay passing 100 ezredmásodpercben, minden alkalommal, amikor csak hívandó. De a valóságban ez valószínűleg jobb, ha az exponenciális visszatartási késleltetés bizonyos típusú használja helyette.

A zárolás igényelve, miután AddAsync hozzáad egy belső átmeneti szótárba, az ITransaction objektumhoz társított kulcs-érték objektum hivatkozását. Ez történik, olvasási-your-saját-írások szemantika használatával. Azt jelenti Miután AddAsync felhívja, (az azonos ITransaction objektum használatával) TryGetValueAsync újabb hívása értéket fogja visszaadni akkor is, ha Ön rendelkezik nem véglegesített tranzakció. Ezután AddAsync szerializálja a kulcs és érték bájttömbökhöz objektumok és ezek bájttömbökhöz fűz a helyi csomópont egy naplófájlba. Végül AddAsync elküldi a bájttömbökhöz a másodlagos replikák úgy, hogy a kulcs/érték ugyanazokat az információkat. Annak ellenére, hogy a kulcs/érték-adatokat egy naplófájl íródott, az adatokat nem részének számít a szótár mindaddig, amíg a velük társított tranzakció véglegesítve.

A fenti kódrészletben CommitAsync meghívásához érvényesítése a tranzakció műveleteket. Pontosabban a véglegesítés adatokat fűz a naplófájl a helyi csomóponton, és a végrehajtási rekord is küld a másodlagos replikákon. A replikák kvórum (nagy) válaszolt, miután minden adatmódosítást állandó számít, és minden társított kulcsokat, amelyekkel a ITransaction objektum-n keresztül is kezelhetők zárolások jelennek meg más szálak/tranzakciók ugyanazokkal a kulcsokkal is módosíthatja, és azok értékek.

CommitAsync (általában egy kivétel) miatt nem hívják meg, ha a ITransaction objektum beolvasása eldobva. Nem véglegesített ITransaction objektum értékesítésére, amikor a Service Fabric hozzáfűzi a megszakítási információkat a helyi csomópont naplófájlt, és semmit nem kell bármelyik másodlagos replikán kell küldeni. És ezt követően jelennek meg minden olyan zárolásokat társított kulcsokat, amelyekkel a tranzakció-n keresztül is kezelhetők.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Közös buktatókat, és hogyan rájuk
Most, hogy már érti, hogyan működnek a megbízható gyűjteményekből belsőleg, vessünk egy pillantást ezek közül néhány közös előreláthatók. Tekintse meg az alábbi kódot:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Az egy normál .NET szótár használatakor egy kulcs/érték hozzáadása a szótár, és majd módosítsa az értéket (például LastLogin) tulajdonság. Azonban ez a kód nem működnek megfelelően az egy megbízható szótárban. Ne feledje a korábbi vitafórum a AddAsync hívása szerializálja a kulcs/érték bájttömbökhöz objektumok, és ezután menti a tömbök egy helyi fájlba, és is elküldi őket a másodlagos replikákon. Ha később megváltoztatja az olyan tulajdonság, a tulajdonság értéke csak; a memóriában változik a helyi fájl vagy a replikákra elküldött adatokat nem érinti. Ha a folyamat leáll, a memória van érvényteleníteni. Amikor új folyamat elindul, vagy ha egy másik replikára elsődleges válik, majd a régi tulajdonság értéke nem érhető el.

E hangsúlyozzák nem elég mennyire egyszerű is, hogy milyen típusú fenti hibát. És csak ismertetik a hibát. Ha a folyamat leáll. A kód írása a megfelelő módon egyszerűen, hogy a két sor fordított:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Íme egy másik gyakori hiba példa:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Újra, a .NET reguláris szótárak, a fenti kód jól működik, és a egy gyakori trend: a fejlesztői kulcsot használ kereshet meg egy értéket. Ha az érték már létezik, a fejlesztői módosítja egy tulajdonság értéke. A megbízható gyűjtemények, hogy ezt a kódot azonban kiváló ugyanez a probléma a már tárgyalt: **ne módosítson egy objektumot után egy megbízható gyűjteményben fordítani.**

A helyes-e frissíteni egy értéket egy megbízható gyűjteményben módja egy hivatkozást a meglévő értéket, és fontolja meg az objektumra hivatkozik Ez a hivatkozás nem módosítható. Ezután hozzon létre egy új objektumot, amely az eredeti objektum pontos másolatát. Most módosíthatja az új objektum állapotát, és írja be a gyűjteménybe. az új objektum úgy, hogy azt a lekérdezi bájttömbökhöz, hogy szerializálni hozzáfűzi a helyi fájl és elküldve a. A módosítás(ok) véglegesítését, a memóriában lévő objektumok, a helyi fájl és minden replika után pontos állapotra. Minden készen áll!

Az alábbi kód bemutatja a megfelelő módon frissíteni egy értéket egy megbízható gyűjteményben:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
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

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Programozói hiba elkerülése érdekében nem módosítható az adattípusok definiálása
Szeretnénk ideális esetben hibák jelentéséhez a fordító, ha véletlenül olyan kódot, amely mutates fontolja meg a nem módosítható, amelyeknek az objektumok állapotát. Azonban a C# fordító nem tudnak ehhez. Így lehetséges programozói hibák elkerülése érdekében javasoljuk, hogy a típust határoznak meg kell típusa nem módosítható a reliable collections használata. Pontosabban Ez azt jelenti, annál maradni core érték típusú (például a számok [Int32, UInt64 stb.], dátum és idő, Guid, időtartam és a hasonló). Karakterlánc is használható. Gyűjtemény tulajdonságait, szerializálása elkerülése érdekében ajánlott, és azokat deszerializálása során gyakran összeállítása hátrányosan befolyásolhatja a teljesítményt. Javasoljuk azonban, ha szeretné használni a gyűjtemény tulajdonságait, magas használatát. A HÁLÓ nem módosítható gyűjteményeket könyvtár ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ebben a könyvtárban letölthető http://nuget.org. Javasoljuk továbbá, az osztályok zárolásra és a mezők csak olvasható, amikor csak lehetséges.

Az alábbi UserInfo típusát mutatja be egy kihasználhatja a fenti javaslatok nem módosítható típusának definiálása.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
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

Az elemazonosító típusa is egy nem módosítható típus Itt látható módon:

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

## <a name="schema-versioning-upgrades"></a>Séma verziószámozásával (frissítések)
A Reliable Collections szerializálni a belsőleg, az objektumok. A NET DataContractSerializer. A szerializált objektumokat is az elsődleges replika helyi lemezen való megőrzése és is eljussanak a másodlagos replikákon. A szolgáltatás kiforrottá, valószínű, milyen típusú adatok (séma), a szolgáltatásnak szüksége van módosítani szeretné. Az adatok bármilyen megközelítés verziókezelését. Mindenekelőtt a mindig kell lehet deszerializálni a régi adatokat. Pontosabban Ez azt jelenti, a deszerializálás kód korlátlanul visszamenőlegesen kompatibilis kell lennie: A szolgáltatás kód 333 verziója adatot egy megbízható gyűjteményben 1. verzióját a szolgáltatást kód 5 éve a művelethez használandó képesnek kell lennie.

Ezenkívül a szolgáltatás kódja: frissített több frissítési tartományt egyszerre. Ezért a frissítés során kell a szolgáltatást kód fut egyidejűleg két különböző verzióit. Az új verziót a szolgáltatás kód az új sémával használja a szolgáltatást kód régi verziói nem feltétlenül tudja kezelni az új sémában kellene kell elkerülése érdekében. Ha lehetséges, akkor tervezzen egy verzió inkompatibilis lehet a szolgáltatás egyes verzióihoz. Pontosabban Ez azt jelenti, hogy a szolgáltatás kód V1 kell figyelmen kívül semmilyen séma elemei nem explicit módon kezeli. Azonban képesnek kell lennie minden olyan, nem explicit módon ismert adatok és írási, biztonsági ki, amikor frissíti egy szótárban kulcs vagy érték mentéséhez.

> [!WARNING]
> Bár a kulcs a séma módosításához biztosítania kell, hogy a kulcs a kivonatkód egyenlő algoritmusok és stabil. Ha módosítja a hogyan működnek ezek az algoritmusok közül választhat, nem tud keresse ki a kulcsot a megbízható szótárban belül bármikor újra.
> Egy kulcs, de maga a karakterlánc, a kulcs – használata nem használnak String.GetHashCode eredményét kulcsként .NET karakterláncokat is használhatók.

Másik lehetőségként hajthat végre, mi van általában úgy nevezik két frissítés. Egy kétfázisú frissítést frissítse a szolgáltatás V1-től a v2: V2 tartalmazza a kódot, amely képes az új sémaváltozás foglalkozik, de ez a kód nem hajtja végre. A V2 kód V1 adatokat olvas be, amikor azt rajta működik, és V1 adatokat ír. Ezt követően a frissítés befejezése után minden frissítési tartományok között, akkor is valamilyen módon jelezze futó V2 példányok, hogy a frissítés befejeződött. (Jel egyik módja a vezethet be a konfiguráció frissítése, ez az egy kétfázisú frissítés teszi ezt.) Most a V2-példányokat is V1-adatok olvasása, alakíthatja át a V2-adatok, a művelethez, és írható ki V2 adatként. További példányok V2-adatok olvasási, nem kell konvertálni, azok csak működik rajta, és kiírja a V2-adatok.

## <a name="next-steps"></a>További lépések
Kompatibilis adatokat továbbítson a szerződések létrehozásával kapcsolatos további információkért lásd: [továbbító-kompatibilis Data szerződések](https://msdn.microsoft.com/library/ms731083.aspx)

Verziókezelés adatok szerződések kapcsolatos bevált gyakorlatok megismeréséhez tekintse meg a [adatok szerződés verziószámozás](https://msdn.microsoft.com/library/ms731138.aspx)

Verzió hibatűrő adatok szerződések megvalósítása kapcsolatban lásd: [verzió hibatűrő szerializálási visszahívások](https://msdn.microsoft.com/library/ms733734.aspx)

Megtudhatja, hogyan tud olyan adatszerkezet, amely képes együttműködni különböző több verziót, tekintse meg a [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)