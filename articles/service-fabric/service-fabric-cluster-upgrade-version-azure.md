---
title: Fürt Azure Service Fabric-verziójának frissítése
description: Frissítse a Service Fabric-kódot és/vagy konfigurációt, amely egy Service Fabric-fürtöt futtat, beleértve a fürtfrissítési mód beállítását, a tanúsítványok frissítését, az alkalmazásportok hozzáadását, az operációs rendszer javításait és így tovább. Mire számíthat a frissítések végrehajtásakor?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: c3ffcbd4296385623ff5e2c1ee001c27598ff3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451809"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Egy fürt Service Fabric-verziójának frissítése

Minden modern rendszer számára a felbonthatóság tervezése kulcsfontosságú a termék hosszú távú sikerének eléréséhez. Az Azure Service Fabric-fürt egy olyan erőforrás, amely az Ön tulajdonában van, de részben a Microsoft kezeli. Ez a cikk ismerteti, hogyan frissítheti az Azure-fürtben futó Service Fabric verzióját.

Beállíthatja, hogy a fürt automatikus hálófrissítéseket kapjon, amint azok a Microsoft által kiadott, vagy kiválaszthatja a támogatott hálóverziót, amelyen a fürthöz be kell lépnie.

Ehhez a "upgradeMode" fürtkonfiguráció beállításával a portálon, vagy az Erőforrás-kezelő használatával a létrehozáskor vagy egy élő fürtön használja az Erőforrás-kezelőt. 

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig támogatott hálóverziót futtat. Ahogy és amikor bejelenti a szolgáltatásháló új verziójának kiadását, az előző verzió a támogatás megszűnése után legalább 60 nap elteltével van megjelölve. Az új kiadások bejelentette [a szolgáltatás szövet csapat blog](https://blogs.msdn.microsoft.com/azureservicefabric/). Az új kiadás akkor választható. 
> 
> 

14 nappal a kiadás lejárta előtt a fürt fut, egy állapotesemény jön létre, amely a fürt egy figyelmeztető állapotba helyezi. A fürt figyelmeztetési állapotban marad, amíg nem frissít egy támogatott hálóverzióra.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>A frissítési mód beállítása az Azure Portalon
A fürt ötös vagy manuális beállítását beállíthatja a fürt létrehozásakor.

![Create_Manualmode][Create_Manualmode]

A fürt ötös vagy manuális, ha egy élő fürtön, a kezelési élmény használatával állítható be. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Frissítés egy új verzióra egy olyan fürtön, amely manuális módra van állítva a portálon keresztül.
Az új verzióra való frissítéshez mindössze annyit kell tennie, hogy kiválasztja a legördülő menüből a rendelkezésre álló verziót, és mentse. A Fabric frissítés automatikusan elindul. A fürt állapotházirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) a frissítés során be vannak tartva.

Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva. Görgessen le a dokumentumon, hogy többet tudjon meg az egyéni állapotházirendek beállításáról. 

Miután kijavította a visszaállítást eredményező problémákat, újra el kell indítania a frissítést, ugyanazokat a lépéseket követve, mint korábban.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>A frissítési mód beállítása Erőforrás-kezelő sablonnal
Adja hozzá a "upgradeMode" konfigurációt a Microsoft.ServiceFabric/clusters erőforrás-definíciójához, és állítsa be a "clusterCodeVersion" értéket a támogatott hálóverziók egyikére az alábbiak szerint, majd telepítse a sablont. A "upgradeMode" érvényes értékei a "Manuális" vagy az "Automatikus"

![ARMUpgradeMode (ARMUpgradeMode)][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Frissítés egy új verzióra egy olyan fürtön, amely Egy Erőforrás-kezelő sablonon keresztül kézi módra van állítva.
Ha a fürt kézi üzemmódban van, új verzióra való frissítéshez módosítsa a "clusterCodeVersion" verziót egy támogatott verzióra, és telepítse azt. A sablon üzembe helyezése, a Fabric-frissítés rúgásai automatikusan elindulnak. A fürt állapotházirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) a frissítés során be vannak tartva.

Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva.  

Miután kijavította a visszaállítást eredményező problémákat, újra el kell indítania a frissítést, ugyanazokat a lépéseket követve, mint korábban.

## <a name="set-custom-health-polices-for-upgrades"></a>Egyéni állapotfigyelő késedelmi dista beállítása a frissítésekhez
Megadhatja az egyéni egészségügyi biztonsági anyagok háló frissítés. Ha a fürtöt automatikus háló-frissítésekre állította, akkor ezek a házirendek az [automatikus hálófrissítések 1.](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)
Ha beállította a fürt manuális háló frissítések, majd ezek a házirendek minden alkalommal, amikor kiválaszt egy új verziót, amely elindítja a rendszert, hogy indítsa el a háló frissítése a fürtben. Ha nem bírálja felül a házirendeket, a rendszer az alapértelmezett értékeket használja.

Megadhatja az egyéni állapotházirendeket, vagy tekintse át az aktuális beállításokat a "háló frissítési" panel alatt, a speciális frissítési beállítások kiválasztásával. Tekintse át az alábbi képet arról, hogyan kell. 

![Egyéni állapotházirendek kezelése][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Az adott előfizetés összes környezetéhez elérhető összes verzió listázása
Futtassa a következő parancsot, és ehhez hasonló kimenetet kell kapnia.

A "supportExpiryUtc" jelzi, ha egy adott kiadás lejár vagy lejárt. A legújabb kiadásnak nincs érvényes dátuma - értéke "9999-12-31T23:59:59.9999999", ami csak azt jelenti, hogy a lejárati dátum még nincs beállítva.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre a [szolgáltatásháló fürthálózatának néhány beállítását](service-fabric-cluster-fabric-settings.md)
* További információ [a fürt méretezése és méretezése](service-fabric-cluster-scale-up-down.md)
* További információ [az alkalmazásfrissítésekről](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
