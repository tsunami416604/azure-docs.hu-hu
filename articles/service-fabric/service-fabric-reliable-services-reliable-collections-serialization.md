---
title: Az Azure Service Fabric Reliable gyűjtemény objektumszerializáló |} A Microsoft Docs
description: Az Azure Service Fabric Reliable Collections objektumszerializáló
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: 48f7153dcee45a6271919ac756ad794186faaed4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668439"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Az Azure Service Fabric Reliable gyűjtemény objektumszerializáló
A Reliable Collections replikálni, és ellenőrizze, hogy a gép hibák és áramkimaradások tartós azok az elemek megőrzéséhez.
Replikálása és elemek is tartalmaz, a Reliable Collections őket szerializálni kell.

A Reliable Collections lekérheti a megfelelő szerializáló adott írja be a következőt a Reliable State Manager.
A Reliable State Manager beépített objektumszerializáló tartalmaz, és lehetővé teszi, hogy kell regisztrálni egy adott típusú egyéni objektumszerializáló.

## <a name="built-in-serializers"></a>Beépített objektumszerializáló

A Reliable State Manager tartalmaz néhány gyakori típusok, beépített szerializálót, úgy, hogy azok szerializálására hatékonyan alapértelmezés szerint. Más fájltípusok a Reliable State Manager visszavált használja a [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Beépített objektumszerializáló még hatékonyabbak, mivel ismerik a típusuk nem módosítható és nem kell például a típusnév típusára vonatkozó információkat tartalmazza.

A Reliable State Manager a következő típusú beépített szerializáló rendelkezik: 
- Guid
- logikai
- bájt
- sbyte
- byte[]
- CHAR
- sztring
- tizedes tört
- double
- lebegőpontos
- int
- uint
- hosszú
- ulong
- rövid
- ushort

## <a name="custom-serialization"></a>Egyéni sorba rendezésre

Egyéni objektumszerializáló gyakran használják, a teljesítmény növelése és a hálózaton és lemezen lévő adatok titkosításához. Egyéb okok mellett egyéni objektumszerializáló általában hatékonyabb, mint az általános szerializáló mivel, nincs szükségük a típus adatainak szerializálásához. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) regisztrálható a megadott típus T. egyéni szerializálót Ez a regisztráció történjen, az annak érdekében, hogy helyreállítás megkezdése előtt az összes megbízható gyűjtemények férhetnek hozzá a megfelelő szerializáló a megőrzött adatok olvasása a StatefulServiceBase kialakításában.

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
> Egyéni objektumszerializáló beépített objektumszerializáló adott elsőbbséget. Például amikor regisztrál egy egyéni szerializálót int, azt van szerializálásához használt egész számok helyett a beépített szerializálót: egész szám.

### <a name="how-to-implement-a-custom-serializer"></a>Egy egyéni szerializáló megvalósítása

Egy egyéni szerializáló kell megvalósítani a [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) felületet.

> [!NOTE]
> IStateSerializer<T> túlterhelés írási és olvasási, amely tartalmaz egy további T alapérték nevű. Az API-t a különbözeti szerializálásra. Jelenleg a különbözeti szerializálási funkció nem lesz közzétéve. Ezért ezek két túlterheléssel nem nevezzük mindaddig, amíg a különbözeti szerializálási kitéve, és engedélyezve.

Az alábbiakban egy példa négy tulajdonságot tartalmazó OrderKey nevű egyéni típus a

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

Az alábbiakban a egy IStateSerializer megvalósítási példát szemléltet<OrderKey>.
Vegye figyelembe, hogy olvasási és írási túlterheléssel, amelyek a baseValue, hívja meg a megfelelő túlterhelési továbbítást kompatibilitás.

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
Az egy [alkalmazás frissítése működés közbeni](service-fabric-application-upgrade.md), a frissítés alkalmazott csomópontok egy része, egyszerre több frissítési tartományt. A folyamat során az alkalmazás újabb verziójában néhány frissítési tartományok lesznek, és néhány frissítési tartományok lesz az alkalmazás régebbi verzióját. A bevezetés során az alkalmazás új verziója, olvassa el a régi verziót, az adatok képesnek kell lennie, és az alkalmazás régebbi verzióját kell tudni olvasni az adatokat az új verziót. Ha a adatformátum nem előre és visszafelé kompatibilis, a frissítés sikertelen lehet, vagy rosszabb, adatok esetleg elveszett vagy sérült állapotba kerül.

Ha beépített szerializáló használ, nem kell aggódnia a kompatibilitási.
Azonban ha egy egyéni szerializáló vagy a DataContractSerializer használ, az adatokat kell korlátlanul előre és visszafelé kompatibilis.
Más szóval a szerializáló minden verziója képesnek kell lennie szerializálható és deszerializálható bármelyik verziója, a típus.

Adatok szerződés felhasználók hozzáadása, eltávolítása és a mezők módosítása a jól definiált versioning szabályokat érdemes követnie. Adatok szerződést is foglalkoznak, ismeretlen mezőket, a szerializálást és deszerializálást folyamat történetének és osztályöröklődés foglalkozó támogatással rendelkezik. További információkért lásd: [adategyezményben használatával](https://msdn.microsoft.com/library/ms733127.aspx).

Egyéni felhasználóknak be kell tartaniuk a szerializáló használják, hogy ez visszafelé, és továbbítja a kompatibilis az útmutatást.
Gyakori módja az összes verzió igazoló méretadatainak hozzáadása elején, és csak a nem kötelező tulajdonságok hozzáadását.
Minden verziója ezzel a módszerrel olvashatja sokkal azt is, és ugorjon a stream hátralévő része felett.

## <a name="next-steps"></a>További lépések
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Az alkalmazás használatával a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítése a Visual Studio használatával.
  * [Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.
  * Vezérelheti, hogyan az alkalmazásfrissítések használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).
  * Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [haladó témakörök](service-fabric-application-upgrade-advanced.md).
  * Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [Alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).
