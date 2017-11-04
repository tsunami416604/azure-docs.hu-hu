---
title: "Az Azure Service Fabric megbízható gyűjtemény objektum szerializálása |} Microsoft Docs"
description: "Az Azure Service Fabric megbízható gyűjtemények objektum szerializálása"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: c14794b71ce7340d9e90a56d781c712e247ded06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Az Azure Service Fabric megbízható gyűjtemény objektum szerializálása
Megbízható gyűjtemények replikálja, és a gép hibák és áramkimaradások tartós szempontjából elemek megmaradnak.
Replikáció és megőrizni az elemeket, megbízható gyűjtemények kell szerializálni azokat.

Megbízható gyűjtemények egy adott típus megfelelő szerializáló beszerzése megbízható állapotkezelője.
Megbízható állapotkezelője beépített objektumszerializáló tartalmazza, és lehetővé teszi, hogy egyéni objektumszerializáló regisztrálása az adott típus.

## <a name="built-in-serializers"></a>Beépített objektumszerializáló

Megbízható állapotkezelője beépített szerializálót néhány általános típust tartalmazza, úgy, hogy azokat hatékonyabban akkor szerializálható alapértelmezés szerint. A más típusú megbízható állapotkezelője visszaáll használja a [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Beépített objektumszerializáló még hatékonyabbak, mivel még ismernie a típusok nem módosítható, és nincs szükségük a típusát, például a típusnév vonatkozó információval.

Megbízható állapotkezelője rendelkezik beépített szerializáló következő esetében: 
- GUID
- logikai érték
- Bájt
- sbyte
- Byte]
- Karakter
- Karakterlánc
- Decimális
- Dupla
- Lebegőpontos
- int
- uint
- hosszú
- ulong
- rövid
- ushort

## <a name="custom-serialization"></a>Egyedi szerializálás

Egyéni objektumszerializáló gyakran használják, a teljesítmény növelése, vagy az adatok titkosítása a hálózaton, és a lemezen. Egyéb okok mellett egyéni objektumszerializáló általában hatékonyabb, mint az általános szerializáló óta, nem kell szerializálni típusára vonatkozó adatok. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) egyéni szerializáló a(z) az adott típus T. regisztrálhatók Ez a regisztráció megtörténik az biztosítja, hogy helyreállítási megkezdése előtt az összes megbízható gyűjtemények a megfelelő szerializáló beolvasni a megőrzött adatok elérésére StatefulServiceBase kialakításában.

```C#
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
> Egyéni objektumszerializáló beépített objektumszerializáló keresztül kap elsőbbséget. Például egyéni szerializáló a(z) int regisztrálása esetén használatos szerializálni egész számok helyett a beépített szerializálót egész szám.

### <a name="how-to-implement-a-custom-serializer"></a>Egyéni szerializáló implementálása

Egyéni szerializáló kell megvalósítania a [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) felületet.

> [!NOTE]
> IStateSerializer<T> egy túlterhelési írási és olvasási, amely fogad egy további T alapértéke nevű tartalmazza. Ez az API a különbözeti szerializálás van. Jelenleg különbözeti szerializálási funkció nem lesz közzétéve. Ezért ezek két túlterhelések nem hívják meg csak különbözeti szerializálási téve, és engedélyezve.

Az alábbiakban látható egy példa egyéni típus neve, amelyben négy tulajdonságok OrderKey

```C#
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

Az alábbiakban látható egy példa végrehajtásának IStateSerializer<OrderKey>.
Vegye figyelembe, hogy olvasási és írási addsortproperty() baseValue, a továbbítást kompatibilitás a megfelelő túlterhelést hívni.

```C#
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
Az egy [működés közbeni frissítés alkalmazás](service-fabric-application-upgrade.md), a frissítés a csomópontok egy alkészlete, egyszerre több frissítési tartományt vonatkozik. A folyamat során néhány frissítési tartományok lesz az alkalmazás újabb verziója, és néhány frissítési tartományt kell az alkalmazás régebbi verzióját. A bevezetés alatt az alkalmazás új verzióját kell fogja tudni elolvasni az adatok a korábbi verziója, és az alkalmazás régebbi verziójához tudja olvasni az adatokat az új verziót kell lennie. Ha az adatformátum nem előre és hátra kompatibilis, a frissítés sikertelen lehet, vagy rosszabb, adatok esetleg elveszett vagy sérült.

Ha a beépített szerializáló használ, nincs kompatibilitási foglalkoznia.
Azonban ha egy egyéni szerializáló vagy a DataContractSerializer használ, az adatokat kell végtelenül lépkedhet kompatibilis.
Más szóval szerializáló verziói képesnek kell lennie a szerializáltuk és bármilyen típusú.

Adatok szerződés felhasználók hozzáadása, eltávolítása és mezők módosítása jól meghatározott versioning szabályait kell követnie. Adategyezmény ismeretlen mezők foglalkoznak, a szerializálás és a deszerializálás folyamatba csatlakoztatás és osztályöröklődést foglalkozó is rendelkezik. További információkért lásd: [adategyezmény használatával](https://msdn.microsoft.com/library/ms733127.aspx).

Egyéni szerializáló felhasználók be kell tartaniuk a szerializáló, ellenőrizze, hogy visszamenőleges és továbbítja a kompatibilis használják az útmutatást.
Gyakori módja az összes verzió támogatására elején mérete információk hozzáadása, és a csak a választható tulajdonságok hozzáadását.
Így minden verzió tud olvasni, sokkal is és az adatfolyam fennmaradó része ugorhat.

## <a name="next-steps"></a>Következő lépések
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.
  * [Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.
  * Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).
  * Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [Speciális témakörök](service-fabric-application-upgrade-advanced.md).
  * Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).
