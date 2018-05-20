---
title: Megbízható gyűjtemények használata |} Microsoft Docs
description: Ismerje meg az ajánlott eljárások megbízható gyűjtemények használata.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="working-with-reliable-collections"></a>A Reliable Collections használata
A Service Fabric a .NET-fejlesztők számára megbízható gyűjtemények keresztül elérhető állapot-nyilvántartó programozási modellt biztosít. Pontosabban a Service Fabric megbízható szótár és megbízható várólista osztályok biztosít. Ha használja ezeket az osztályokat, az állapot (méretezhetőségre) particionálva, replikálni (a rendelkezésre állás érdekében), és egy partíciót (ACID szemantikáját) belül. Most egy tipikus használati megbízható dictionary objektum tekintse meg, és tekintse meg, milyen a végrehajtása.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Összes művelet (kivéve a ClearAsync pedig nem vonható vissza), a megbízható szótár objektumokon ITransaction objektum szükséges. Ez az objektum van társítva a, és minden bármely megbízható szótár és megbízható végrehajtja a végrehajtani kívánt módosítás várólistára objektumok belül egyetlen partícióra. Egy ITransaction szerez be a partíció meghívásával objektum által StateManager tartozó CreateTransaction metódust.

A fenti kódot, a megbízható dictionary AddAsync metódusnak átadott a ITransaction objektum. Belső a szótár módszerek, amely fogad egy kulcs egy kulcshoz hozzárendelt olvasási/írási zárolás igénybe vehet. A metódus módosítja a kulcs-érték, ha a módszer egy írási zárolás veszi a kulcsot, és ha a metódus csak beolvassa a kulcs értékét, majd olvasási zárolás szükséges a kulcsot. Mivel AddAsync a kulcs-érték az új, az átadott értékre módosítja, a kulcs írási zárolás lesz végrehajtva. Igen 2 (vagy több) szálak megpróbálnak értékek hozzáadásához ugyanazzal a kulccsal egyszerre, egy szál arányban fogják beszerezni az írási zárolás, és a más szálak blokkolja. Alapértelmezés szerint 4 másodpercet a zárolás; módszerek letiltása 4 másodpercen belül a módszerek throw egy TimeoutException. Módszer túlterhelések létezik, hogy lehetővé teszi a adjon át egy explicit időtúllépési értéket, ha inkább.

Általában, hogy a kód írása reagálni a TimeoutException alatt, és újra próbálkozik a teljes műveletet (ahogy a fenti kódot) Egyszerű kód Task.Delay átadásakor 100 milliomod másodperc minden alkalommal, amikor csak hívandó. De a valóságban, valószínűleg jobb, ha valamilyen exponenciális vissza az indító késleltetés használja helyette.

A zárolás keletkezik, ha a AddAsync hozzáadja egy belső ideiglenes könyvtár az ITransaction objektumhoz rendelt kulcs-érték objektumra hivatkozik. Ez biztosítja, hogy olvasási-a-saját-írási műveletek szemantikájú történik. Ez azt jelenti, hogy után hívható AddAsync, (az azonos ITransaction objektum használatával) TryGetValueAsync újabb hívásakor visszatér az érték akkor is, ha még nem véglegesített tranzakció. A következő AddAsync rendezi sorba a kulcsot és értéket bájt tömbök objektumok és ezek bájt tömbök hozzáfűz egy naplófájlba, a helyi csomóponton. Végezetül AddAsync küld a byte tömbök a másodlagos replikák úgy, hogy a kulcs/érték ugyanazokat az információkat. Annak ellenére, hogy a kulcs/érték adatokat naplófájlba van írva, az adatokat nem részének számít a szótár csak a velük társított tranzakció véglegesítése után.

A fenti kódot CommitAsync hívása véglegesíti a tranzakció műveleteket. Pontosabban hozzáfűzi a véglegesítési információt a naplófájl a helyi csomóponton, és a végrehajtási rekord is küld a másodlagos replikákon. Miután a replikák másodlagosak (nagy) rendelkezik válaszolt, minden adatmódosítást állandó minősülnek, és bármely, amely nem volt kezelhetők a ITransaction objektum keresztül kapcsolódó zárolások feloldásáig blokkolva lesz, így más szálak tranzakciók állíthatók be ugyanazokkal a kulcsokkal és értékekkel.

Ha CommitAsync nem neve (általában miatt kivételt folyamatban), majd a ITransaction elemet lekérdezi eldobták. Nem véglegesített ITransaction objektum ártalmatlanítása, amikor a Service Fabric hozzáfűzi a megszakítási információt a helyi csomópont naplófájl, és semmi nem kell a másodlagos replikák bármelyike elküldését. És ezt követően bármely, amely nem volt kezelhetők a tranzakció keresztül kapcsolódó zárolások feloldásáig blokkolva lesz.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Közös nehézségek és azok elkerülésének
Most, hogy megismerte a megbízható gyűjtemények működése belső, vessen egy pillantást néhány gyakori előreláthatók őket. Tekintse meg az alábbi kódot:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Egy rendszeres .NET könyvtár az használatakor egy kulcs/érték hozzáadása a szótár, és módosítsa a tulajdonság (például LastLogin). Azonban ez a kód nem működnek megfelelően megbízható adatkönyvtárhoz. Ne felejtse el a korábbi vitafórum a AddAsync hívása rendezi sorba a kulcs/érték objektumok bájt tömbök és a helyi fájl majd menti a tömbök és is elküldi azokat a másodlagos replikákon. Ha később megváltoztatja egy tulajdonság, a tulajdonság értéke csak; memóriában változik a helyi fájl vagy a replikák küldött adatok nem befolyásolja. Ha a folyamat leállásából eredő, a memória van érvényteleníteni. Új folyamat indításakor, vagy ha egy másik replika elsődleges válik, majd a régi tulajdonság értéke nem elérhető.

I nem emelje ki elég milyen egyszerűen azt, hogy milyen típusú hibát fent látható. És csak megtudhatja, hogyan a hibát. Ha a folyamat leáll. A kód írása a megfelelő módon egyszerűen a két sort fordított:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Íme egy másik példa a megjelenítő gyakori hiba:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Ebben az esetben rendszeres .NET szótárak, a fenti kódot remekül működik, és egy közös minta: a fejlesztői kulcsot használ kereshet meg egy értéket. A meglévő értéket, a fejlesztői módosítja egy tulajdonság értékét. Megbízható gyűjteményéhez, hogy ez a kód azonban mutat a szerint már tárgyalt probléma: **ne módosítson egy objektum után adott, megbízható gyűjteményhez.**

A megfelelő értéket egy megbízható gyűjtemény frissítése módja egy hivatkozást a meglévő értéket, és fontolja meg többé ez az útmutató által hivatkozott objektum. Ezután hozzon létre egy új objektumot, amely az eredeti objektum pontos másolatát. Most ezt az új objektumot állapotának módosítása, és írja a gyűjteményhez az új objektumot, hogy azt bájt tömbök, szerializálható lekérdezi a hozzáfűzi a helyi fájlhoz, és elküldve a. A módosítás(ok) véglegesítés után a memóriában lévő objektumok, a helyi fájl és a replikák a azonos pontos állapottal rendelkeznek. Csak jó!

Az alábbi kódot a megfelelő módon frissíteni egy megbízható gyűjtemény értéket jeleníti meg:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Programozói hiba megelőzése érdekében megváltoztathatatlan adattípusok definiálása
Szeretnénk ideális esetben a fordítási hibákat, ha véletlenül eredményez, amelyeknek figyelembe kell venni az nem módosítható objektum állapotának mutates kódot. De a C# fordítóprogram nincs ehhez lehetőséget. Igen, lehetséges programozói hibák elkerülése érdekében határozottan ajánlott, hogy a típust határoznak meg kell megváltoztathatatlan típusokra megbízható gyűjteményeket használ. Pontosabban Ez azt jelenti, hogy anyagot core értéktípusok (például számok [Int32, UInt64 stb.] dátum és idő, Guid, TimeSpan érték vagy hasonló). És természetesen is karakterlánc használható. Legjobb gyűjtemény tulajdonságok szerializálása során elkerülése érdekében, és azokat deszerializálása is gyakran hátrányosan befolyásolhatja a teljesítményt. Azonban ha gyűjteménytulajdonságokkal használni kívánt, erősen ajánlott a használata. NET által nem módosítható gyűjteményeket könyvtár ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ebben a könyvtárban letölthető http://nuget.org. Javasoljuk továbbá, az osztályok zárolásra és a mezőket csak olvasható amikor csak lehetséges.

Az alábbi UserInfo típus bemutatja, hogyan kell egy nem módosítható típus kihasználja a fenti ajánlásokat.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
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
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

A ItemId típusa nem is módosítható típus Itt látható módon:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Séma versioning (frissítés)
Belsőleg megbízható gyűjtemények szerializálni a objektumok használják. NET a DataContractSerializer. A szerializált objektumok őrződnek meg az elsődleges másodpéldány helyi lemezre, és a rendszer szintén továbbíthatja a Microsoftnak a másodlagos replikákon. A szolgáltatás Miután kiforrottá válik, valószínű, milyen típusú adatok (séma), a szolgáltatásnak szüksége van módosítani kell. Nagy gondot az adatok verziószámozásának kell készíthető elő. Mindenekelőtt mindig kell tudni régi adatokat. Pontosabban, ez azt jelenti, hogy a deszerializálás kódot kell végtelenül visszamenőlegesen kompatibilis: a szolgáltatás kód verziója 333 egy megbízható gyűjtemény helyezi el őket a szolgáltatáskód hibáit 1 verziójának 5 éve adatok alapján képesnek kell lennie.

Ezenkívül szolgáltatáskódot egyszerre lehet frissített több frissítési tartományt. Igen a frissítés során, hogy a szolgáltatáskód hibáit, hiszen egyszerre két különböző verziója. A szolgáltatáskód hibáit új verzióját használja az új sémával, mert a szolgáltatáskód hibáit régi verziói esetleg nem tudja kezelni az új sémával rendelkező kell elkerülése érdekében. Ha lehetséges, akkor tervezzen előre kompatibilis 1 verziójával kell a szolgáltatás minden verziója. Pontosabban Ez azt jelenti, hogy 1-es verzió, a szolgáltatás kód egyszerűen figyelmen kívül bármely séma elemei nem explicit módon kezeli képesnek kell lennie. Azonban képesnek kell lennie minden explicit módon kapcsolatos nem tud adatot és egyszerűen vissza kimenő szótár kulcs vagy érték frissítésekor írási mentéséhez.

> [!WARNING]
> Amíg a séma, kulcs módosíthatja, bizonyosodjon meg, hogy a kulcs kivonatkód és egyenlő algoritmusok stabil. Ha módosítja, hogyan működnek ezek az algoritmusok valamelyikét, csak akkor képes a kulcsot a megbízható szótár belül minden eddiginél újra.
>
>

Másik lehetőségként hajthat végre, milyen gyakran emlegetik úgy, a 2-fázis frissítés. A 2-fázis frissítést, a szolgáltatás V1-es rendszerről frissít V2: V2 a kódot tartalmaz, amely meg tudja az új sémaváltozás foglalkozik, de ez a kód nem hajtja végre. A V2 kód V1 adatok olvasását, akkor működik, és V1 adatokat. Majd a frissítés befejezése után minden frissítési tartományok között, akkor is valamilyen módon azt a futó V2-példányokban kívánja, hogy a frissítés befejeződött. (Jel egyik módja azt a konfigurációs frissítés fokozatosan; ez hasznossá ezt a 2-fázis frissítésének.) Most a V2 példányok is V1-adatok olvasása, V2 adatokat átalakíthatja, el és kiírni V2 adatként. Ha más esetekben V2-adatok olvasása, nem kell konvertálni, csak működik rajta, és kiírni a V2-adatok.

## <a name="next-steps"></a>További lépések
Kompatibilis adatokat továbbítson a szerződések létrehozásával kapcsolatos további tudnivalókért lásd: [előre kompatibilis adategyezményeinek](https://msdn.microsoft.com/library/ms731083.aspx).

Gyakorlati tanácsok a versioning adategyezményeinek kapcsolatban [adatok szerződés Versioning](https://msdn.microsoft.com/library/ms731138.aspx).

Verzió hibatűrő adategyezményeinek végrehajtására, lásd: [verzió hibatűrő szerializálási visszahívások](https://msdn.microsoft.com/library/ms733734.aspx).

Adjon meg olyan adatszerkezet, amely képes együttműködni több verziója között lásd: [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
