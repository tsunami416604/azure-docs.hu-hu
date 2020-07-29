---
title: Az Azure Migrate-berendezés architektúrája
description: Áttekintést nyújt a kiszolgálók értékeléséhez és áttelepítéséhez használt Azure Migrate készülékről.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 0752f7afa7ff8d25f938084fd9e6e863d885f9aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770900"
---
# <a name="azure-migrate-appliance-architecture"></a>Az Azure Migrate-berendezés architektúrája

Ez a cikk a Azure Migrate berendezés architektúráját és folyamatait ismerteti. Az Azure Migrate készülék egy helyileg üzembe helyezett, kis üzemben lévő berendezés, amely a virtuális gépeket és fizikai kiszolgálókat észleli az Azure-ba való Migrálás során. 

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate készüléket a következő esetekben használja a rendszer.

**Forgatókönyv** | **Eszköz** | **Alkalmazási cél** 
--- | --- | ---
**VMware virtuális gép értékelése** | Azure Migrate: kiszolgáló értékelése | VMware virtuális gépek felderítése.<br/><br/> Gépi alkalmazások és függőségek felderítése.<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak összegyűjtése, valamint az Azure-ba való küldés.
**VMware virtuális gép migrálása (ügynök nélkül)** | Azure Migrate: kiszolgáló áttelepítése | VMware virtuális gépek felderítése<br/><br/>  VMware virtuális gépek replikálása [ügynök nélküli áttelepítéssel](server-migrate-overview.md).
**Hyper-V virtuális gépek felmérése** | Azure Migrate: kiszolgáló értékelése | Hyper-V virtuális gépek felderítése.<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak összegyűjtése, valamint az Azure-ba való küldés.
**Fizikai gép** |  Azure Migrate: kiszolgáló értékelése |  Fizikai kiszolgálók felderítése.<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak összegyűjtése, valamint az Azure-ba való küldés.

## <a name="appliance-components"></a>Berendezés összetevői

A készülék számos összetevővel rendelkezik.

- **Felügyeleti alkalmazás**: ez egy webalkalmazás felhasználói bevitelhez a készülék üzembe helyezése során. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.
- **Felderítési ügynök**: az ügynök a számítógép-konfigurációs adatokat gyűjti. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos. 
- **Értékelő ügynök**: az ügynök teljesítményadatokat gyűjt. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.
- **DRA-ügynök**: a virtuális gépek replikálását és a replikált számítógépek és az Azure közötti kommunikáció koordinálását koordinálja. Csak akkor használható, ha a VMware virtuális gépeket az Azure-ba replikálja ügynök nélküli Migrálás használatával.
- **Átjáró**: replikált adatokat küld az Azure-nak. Csak akkor használható, ha a VMware virtuális gépeket az Azure-ba replikálja ügynök nélküli Migrálás használatával.
- **Automatikus frissítési szolgáltatás**: a készülék összetevőinek frissítése (24 óránként fut).



## <a name="appliance-deployment"></a>Berendezések üzembe helyezése

- A Azure Migrate készülék a [Hyper-V](how-to-set-up-appliance-hyper-v.md) vagy a [VMware](how-to-set-up-appliance-vmware.md) sablon használatával, vagy a [VMware/Hyper-v](deploy-appliance-script.md)-hez készült PowerShell-parancsfájl-telepítővel, valamint a [fizikai kiszolgálók](how-to-set-up-appliance-physical.md)esetében állítható be. 
- A berendezések támogatásának követelményeit és az üzembe helyezés előfeltételeit a [készülék támogatási mátrixa](migrate-appliance.md)összegzi.


## <a name="appliance-registration"></a>Készülék regisztrálása

A készülék telepítése során regisztrálja a készüléket Azure Migrate, és a táblázatban összefoglalt műveletek bekövetkeznek.

**Művelet** | **Részletek** | **Engedélyek**
--- | --- | ---
**Forrásoldali szolgáltatók regisztrálása** | Ezek az erőforrás-szolgáltatók regisztrálva vannak a készülék beállítása során kiválasztott előfizetésben: Microsoft. OffAzure, Microsoft. Migrál és Microsoft. kulcstartó.<br/><br/> Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. | Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetésben.
**Azure AD-alkalmazás létrehozása – kommunikáció** | A Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást a készüléken futó ügynökök és az Azure-on futó megfelelő szolgáltatások közötti kommunikációhoz (hitelesítéshez és engedélyezéshez).<br/><br/> Az alkalmazásnak nincs jogosultsága Azure Resource Manager hívásokat kezdeményezni, vagy RBAC hozzáférést bármilyen erőforráshoz. | A Azure Migrate az alkalmazás létrehozásához szükséges [engedélyekkel](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps) kell rendelkeznie.
**Azure AD-alkalmazások létrehozása – Key Vault** | Ez az alkalmazás csak a VMware virtuális gépeknek az Azure-ba irányuló ügynök nélküli áttelepítéséhez jön létre.<br/><br/> Kizárólag az ügynök nélküli áttelepítés felhasználói előfizetésében létrehozott kulcstartó elérésére szolgál.<br/><br/> A RBAC hozzáférése az Azure Key vaultban (az ügyfél bérlője alapján jött létre), ha a felderítést kezdeményezik a készülékről. | A Azure Migrate az alkalmazás létrehozásához szükséges [engedélyekkel](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) kell rendelkeznie.



## <a name="collected-data"></a>Összegyűjtött adatok

Az ügyfél által az összes központi telepítési forgatókönyvhöz gyűjtött adatokat a [készülék támogatási mátrixa](migrate-appliance.md)összegzi.

## <a name="discovery-and-collection-process"></a>Felderítési és gyűjtési folyamat

![Architektúra](./media/migrate-appliance-architecture/architecture.png)

A készülék a következő eljárással kommunikál a vCenter-kiszolgálókkal és a Hyper-V-gazdagépekkel/-fürtökkel.

1. **Felderítés elindítása**:
    - Amikor elindítja a felderítést a Hyper-V berendezésen, az a 5985-as (HTTP) és a 5986 (HTTPS) WinRM-portok Hyper-V-gazdagépekkel kommunikál.
    - Amikor elindítja a felderítést a VMware készüléken, az a 443-as TCP-porton keresztül kommunikál a vCenter-kiszolgálóval. Ha a vCenter-kiszolgáló egy másik portot figyel, konfigurálhatja azt a berendezés webalkalmazásban.
2. **Metaadatok és teljesítményadatok összegyűjtése**:
    - A készülék egy CIM-(CIM-) munkamenetet használ a Hyper-V virtuális gépek adatainak a Hyper-V-gazdagépről a 5985-es és 5986-es portokon való összegyűjtéséhez.
    - A készülék alapértelmezés szerint az 443-as porttal kommunikál a VMware virtuális gépek adatainak a vCenter Serverból való összegyűjtéséhez.
3. **Adatküldés**: a készülék elküldi az összegyűjtött adatokat Azure Migrate kiszolgáló értékelésére és Azure Migrate kiszolgáló áttelepítésére az 443-as SSL-porton keresztül. A készülék az interneten keresztül tud csatlakozni az Azure-hoz, vagy használhatja a ExpressRoute nyilvános/Microsoft-partneri kapcsolattal is.
    - A teljesítményadatok esetében a készülék valós idejű kihasználtsági adatokat gyűjt.
        - A teljesítményadatokat a rendszer minden egyes teljesítménymutató esetében 20 másodpercenként gyűjti a VMware-hez, és minden 30 másodpercenként a Hyper-V-hez.
        - Az összegyűjtött adatokat a rendszer összesíti, hogy egy adatpontot 10 percen belül hozzon létre.
        - A csúcsérték kihasználtsági értéke a 20/30 másodperces adatpontból van kiválasztva, és az Azure-ba az értékelés kiszámításához lett küldve.
        - Az értékelés tulajdonságaiban (50/90/95./esetek 99%) megadott százalékos érték alapján a tíz perces pont növekvő sorrendbe kerül, és az értékelés kiszámításához a megfelelő percentilis értéket használja a rendszer.
    - A kiszolgáló áttelepítése esetén a készülék elkezdi a virtuális gépekkel kapcsolatos adatok gyűjtését, és replikálja azt az Azure-ba.
4. **Értékelés és Migrálás**: most már létrehozhat értékeléseket a készülék által gyűjtött metaadatokból Azure Migrate Server Assessment használatával. Emellett a VMware virtuális gépek áttelepítését is megkezdheti Azure Migrate kiszolgáló áttelepítésével az ügynök nélküli virtuális gép replikálásának előkészítéséhez.





## <a name="appliance-upgrades"></a>Berendezések frissítése

A készülék frissítve lett, mivel a készüléken futó Azure Migrate-ügynökök frissülnek. Ez automatikusan megtörténik, mivel alapértelmezés szerint engedélyezve van az automatikus frissítés a készüléken. Az alapértelmezett beállítás módosításával manuálisan is frissítheti az ügynököket.

Ha kikapcsolja az automatikus frissítést a beállításjegyzékben, állítsa a HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" kulcsot 0 (DWORD) értékre.

 

## <a name="next-steps"></a>További lépések

[Tekintse át](migrate-appliance.md) a készülék támogatási mátrixát.

