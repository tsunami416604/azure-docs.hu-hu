---
title: Megbízható gyűjteményobjektum-szerializálás
description: Ismerje meg az Azure Service Fabric megbízható gyűjtemények objektumszerializálását, beleértve az alapértelmezett stratégiát és az egyéni szerializálás meghatározásának módját."
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639547"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Megbízható gyűjteményobjektum-szerializálás az Azure Service Fabricben
A Reliable Collections replikálja és megőrzi az elemeket, hogy megbizonyosodjon arról, hogy azok a géphibák és áramkimaradások között tartósak.
Replikálnia és megpersistni az elemeket, megbízható gyűjtemények kell szerializálni őket.

Megbízható gyűjtemények lekéri a megfelelő szerializáló egy adott típusú megbízható állapotkezelő.
A Reliable State Manager beépített szerializálókat tartalmaz, és lehetővé teszi az egyéni szerializálók regisztrálását egy adott típushoz.

## <a name="built-in-serializers"></a>Beépített szerializálók

Megbízható állapotkezelő beépített szerializáló néhány gyakori típusok, így azok szerializálása hatékonyan alapértelmezés szerint. Más típusok esetében a Reliable State Manager visszaáll a [DataContractSerializer használatára.](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)
A beépített szerializálók hatékonyabbak, mivel tudják, hogy a típusuk nem változhat, és nem kell megadniuk a típusra vonatkozó információkat, például a típusnevét.

A Reliable State Manager beépített szerializálóval rendelkezik a következő típusokhoz: 
- Guid
- Bool
- bájt
- sbyte
- bájt[]
- Char
- sztring
- tizedes tört
- double
- lebegőpontos
- int
- uint
- long
- ulong
- Rövid
- ushort

## <a name="custom-serialization"></a>Egyéni szerializálás

Az egyéni szerializálókat általában a teljesítmény növelésére vagy az adatok vezetékes és lemezes titkosítására használják. Többek között, egyéni szerializálóáltalában hatékonyabbak, mint a generikus szerializáló, mivel nem kell szerializálni információt a típus. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) egyéni szerializáló regisztrálására szolgál a megadott T típushoz. Ez a regisztráció a StatefulServiceBase építése során történik annak érdekében, hogy a helyreállítás megkezdése előtt minden megbízható gyűjtemény hozzáférhessen a megfelelő szerializálóhoz a megőrzött adatok olvasásához.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Az egyéni szerializálók elsőbbséget élveznek a beépített szerializálókkal szemben. Ha például egy egyéni szerializáló int regisztrálva van, akkor szerializálja egész számok helyett a beépített szerializáló int.

### <a name="how-to-implement-a-custom-serializer"></a>Egyéni szerializáló implementálása

Az egyéni szerializálónak végre kell hajtania az [IStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) felületet.

> [!NOTE]
> IStateSerializer\<T> tartalmaz egy túlterhelés az írásés olvasás, amely egy további T nevű alapérték. Ez az API a különbözeti szerializálás. Jelenleg a különbözeti szerializálási szolgáltatás nincs elérhető. Ezért ezt a két túlterhelést nem hívják meg, amíg a különbözeti szerializálás elérhetővé és nem engedélyezve van.

A következő egy példa az OrderKey nevű egyéni típusra, amely négy tulajdonságot tartalmaz

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Az alábbiakban az IStateSerializer\<OrderKey> implementációját mutatbe.
Vegye figyelembe, hogy a baseValue-t használó olvasási és írási túlterhelések meghívják a megfelelő túlterhelést a továbbítási kompatibilitás érdekében.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Bővíthetőség
A [működés közbeni alkalmazásfrissítés](service-fabric-application-upgrade.md)során a frissítés a csomópontok egy részhalmazára vonatkozik, egyszerre egy frissítési tartományra. A folyamat során egyes frissítési tartományok az alkalmazás újabb verzióján lesznek, és néhány frissítési tartomány az alkalmazás régebbi verzióján lesz. A bevezetés során az alkalmazás új verziójának képesnek kell lennie az adatok régi verziójának olvasására, és az alkalmazás régi verziójának képesnek kell lennie az adatok új verziójának olvasására. Ha az adatformátum nem előre- és visszafelé kompatibilis, a frissítés sikertelen lehet, vagy ami még rosszabb, az adatok elveszhetnek vagy megsérülhetnek.

Ha beépített szerializálót használ, nem kell aggódnia a kompatibilitás miatt.
Ha azonban egyéni szerializálót vagy DataContractSerializert használ, az adatoknak végtelenül visszafelé és előre kompatibilisnek kell lenniük.
Más szóval, minden egyes verzió szerializáló képesnek kell lennie arra, hogy szeriaializálja és de-szerializálja bármely változata a típus.

Az adategyezmény-felhasználóknak követniük kell a mezők hozzáadására, eltávolítására és módosítására vonatkozó, jól meghatározott verziószámozási szabályokat. Az adategyezmény támogatja az ismeretlen mezők kezelését, a szerializálási és deszerializálási folyamatba való beakasztást, valamint az osztályöröklés kezelését. További információ: [Using Data Contract](https://msdn.microsoft.com/library/ms733127.aspx).

Egyéni szerializáló felhasználók nak be kell tartaniuk az irányelveket a szerializáló általuk használt, hogy megbizonyosodjon arról, hogy visszafelé és előre kompatibilis.
Az összes verzió támogatásának gyakori módja a méretadatok hozzáadása az elején, és csak a választható tulajdonságok hozzáadása.
Így minden verzió tud olvasni annyi tud, és ugorj át a fennmaradó része a patak.

## <a name="next-steps"></a>További lépések
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Az alkalmazás Visual Studio használatával történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti az alkalmazás frissítésén a Visual Studió használatával.
  * [Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.
  * A frissítési paraméterek használatával szabályozhatja, hogy az alkalmazás hogyan [frissítsen.](service-fabric-application-upgrade-parameters.md)
  * A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.
  * Az alkalmazásfrissítések gyakori problémáinak megoldása az [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva.
