---
title: Fogalmak – identitás és hozzáférés
description: Ismerje meg az Azure VMware-megoldás identitás-és hozzáférési fogalmait (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce85b60b38db86af14b9e9aa06e568436dc76658
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740953"
---
# <a name="azure-vmware-solution-avs-identity-concepts"></a>Az Azure VMware megoldás (AVS) identitásával kapcsolatos fogalmak

A vCenter-kiszolgáló és a NSX-T kezelő kiépítése a privát felhő telepítésekor történik. A vCenter használatával felügyelheti a virtuális gépek számítási feladatait és a NSX-T kezelőt a saját felhőalapú szoftver által meghatározott hálózat kibővítéséhez.

A hozzáférés és az Identitáskezelés a CloudAdmin csoport jogosultságait használja a vCenter és a korlátozott rendszergazdai jogokkal a NSX-T Manager számára. Ez a szabályzat biztosítja, hogy a saját felhőalapú platformja automatikusan frissíthető legyen. Ez rendszeresen biztosítja a legújabb funkciókat és javításokat. A privát felhő frissítéseivel kapcsolatos további részletekért tekintse meg a [Private Cloud Upgrades fogalmait ismertető cikket][concepts-upgrades] .

## <a name="vcenter-access-and-identity"></a>vCenter-hozzáférés és-identitás

A vCenter lévő jogosultságok a CloudAdmin csoporton keresztül érhetők el. Ez a csoport helyileg felügyelhető a vCenter-ben, vagy a vCenter LDAP egyszeri bejelentkezés integrálásával Azure Active Directory használatával. Ezzel a lehetőséggel engedélyezheti az integrációt a privát felhő üzembe helyezése után.

A CloudAdmin és a CloudGlobalAdmin jogosultság az alábbi táblázatban látható.

|  Jogosultság-készlet           | CloudAdmin | CloudGlobalAdmin | Megjegyzés |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Riasztások                  | A CloudAdmin-felhasználók riasztási jogosultságokkal rendelkeznek a számítási ResourcePool és a virtuális gépeken.     |          --        |  -- |
|  Automatikus üzembe helyezés             |  --  |        --        |  A Microsoft üzemelteti a gazdagépek felügyeletét.  |
|  Tanúsítványok            |  --  |        --       |  A Microsoft végzi a Tanúsítványkezelőt.  |
|  Tartalomtár         | Egy CloudAdmin-felhasználónak van jogosultsága fájlok létrehozására és használatára a dokumentumtárakban.    |         Engedélyezve az SSO-val.         |  A Microsoft a tartalmi könyvtárban lévő fájlokat a ESXi-gazdagépek számára terjeszti.  |
|  Adatközpont              |  --  |        --          |  A Microsoft az összes adatközpont-műveletet elvégzi.  |
|  Adattár               | Adattár. AllocateSpace, adattár. Tallózás, adattár. config, adattár. DeleteFile, adattár. FileManagement, adattár. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX-ügynök kezelője       |  --  |         --       |  A Microsoft minden műveletet elvégez.  |
|  Mappa                  |  A CloudAdmin-felhasználók minden mappa jogosultsággal rendelkeznek.     |  --  |  --  |
|  Globális                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global. SystemTag         |                  |    |
|  Gazdagép                    |  Host. HBR. HbrManagement      |        --          |  A Microsoft minden más gazdagép-műveletet hajt végre.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Network (Hálózat)                 |  Network. assign    |                  |  A Microsoft minden más hálózati műveletet hajt végre.  |
|  Engedélyek             |  --  |        --       |  A Microsoft minden engedély műveletet végez.  |
|  Profil alapú tárolás  |  --  |        --       |  A Microsoft minden profillal kapcsolatos műveletet végez.  |
|  Erőforrás                |  A CloudAdmin-felhasználók minden erőforrás-jogosultsággal rendelkeznek.        |      --       | --   |
|  Ütemezett feladat          |  A CloudAdmin-felhasználók minden ScheduleTask jogosultsággal rendelkeznek.   |   --   | -- |
|  Munkamenetek                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  A Microsoft minden más munkamenet-műveletet hajt végre.  |
|  Tárolási nézetek           |  StorageViews. View   |        --          |  A Microsoft az összes többi tárolási nézeti műveletet (szolgáltatás konfigurálása) végzi.  |
|  Feladatok                   |  --  |  --   |  A Microsoft kezeli a feladatokat kezelő bővítményeket.  |
|  vApp                    |  A CloudAdmin-felhasználók minden vApp jogosultsággal rendelkeznek.  |  --  |  --  |
|  Virtuális gép         |  A CloudAdmin-felhasználók minden VirtualMachine jogosultsággal rendelkeznek.  |  --  |  --  |
|  vService                |  A CloudAdmin-felhasználók minden vService jogosultsággal rendelkeznek.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX – T Manager-hozzáférés és-identitás

A NSX-T Manager a "rendszergazda" fiók használatával érhető el. Ez a fiók teljes körű jogosultságokkal rendelkezik, és lehetővé teszi T1 útválasztók, logikai kapcsolók és minden szolgáltatás létrehozását és kezelését. A NSX-T teljes körű jogosultságai biztosítják a NSX-T T0 útválasztó elérését is. A t0 útválasztóra való váltás a hálózat teljesítményének csökkenését vagy a privát felhőhöz való hozzáférés elvesztését eredményezheti. A támogatási követelmények teljesítéséhez szükség van arra, hogy egy támogatási kérést nyisson meg a Azure Portalban, hogy az NSX-T T0-útválasztón bármilyen módosítást igényeljen.
  
## <a name="next-steps"></a>További lépések

A következő lépés a [saját Felhőbeli frissítési fogalmak][concepts-upgrades]megismerése.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md