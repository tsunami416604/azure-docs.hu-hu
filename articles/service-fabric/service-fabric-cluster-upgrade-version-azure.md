---
title: Egy Azure Service Fabric-fürt frissítése |} A Microsoft Docs
description: Frissítés a Service Fabric-kód és/vagy a Service Fabric-fürt, beleértve a fürt frissítési mód, frissítése, tanúsítványok, az alkalmazás portok, operációsrendszer-javítások, ezzel hozzáadása beállítása futtató konfiguráció, és így tovább. Mi a számíthat, ha a frissítés történik?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 3e71199c19fffae0bb7dfa6a59245f1d43cd5065
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855125"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>A fürt Service Fabric-verziójának frissítése

Minden modern rendszeren bővíthetőség for designing fontos a termék hosszú távú sikert eléréséhez. Azure Service Fabric-fürt egy erőforrás, amelynek a tulajdonosa, de részben a Microsoft felügyeli. Ez a cikk ismerteti az Azure-fürtben futó Service Fabric-verziójának frissítése.

Beállíthatja, hogy a fürt automatikus hálófrissítések fogadásához, azok a Microsoft által kiadott, vagy kiválaszthat egy azt szeretné, hogy a rendszer a fürt támogatott fabric verziója.

Ehhez az "upgrademode tulajdonság" fürtkonfiguráció beállítása a portálon, vagy a létrehozás vagy később egy élő fürthöz idején a Resource Manager használatával 

> [!NOTE]
> Győződjön meg arról, hogy a fürt támogatott fabric verziója mindig működik. És hogy jelentjük be az új verzióját a service fabric, az előző verzió után 60 napon, hogy legalább támogatásuk van megjelölve. Az új kiadásokkal már bejelentettünk [a service fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/). Az új kiadásban érhető el, majd válassza ki. 
> 
> 

a fürt fut, a kiadás lejárta előtt 14 nappal állapottal kapcsolatos esemény jön létre, amely a fürt beteszi a figyelmeztetési állapot. A fürt figyelmeztetési állapotban marad, amíg nem támogatott fabric-verzióra frissít.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>A frissítési mód beállítása az Azure Portalon
A fürt és megadható az automatikus vagy kézi-e a fürt létrehozásakor.

![Create_Manualmode][Create_Manualmode]

Beállíthatja a fürt automatikus vagy manuális egy élő fürthöz, a kezelés tapasztalatok használatával. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Egy fürtöt, amely a portálon keresztül manuális módra van beállítva az új verzióra frissíti.
Az új verzióra frissít, ehhez szüksége a rendelkezésre álló verzió válassza a legördülő listából, majd mentse. A Fabric frissítés automatikusan lekéri kezdődjön. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Görgessen lefelé a dokumentum további ezeket egyéni házirendek beállításával. 

Miután javította a problémákat, amelyek a visszaállítás eredményezett, a frissítés ismét kezdeményezése a következő ugyanazokat a lépéseket előtt kell.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>A Resource Manager-sablon használatával frissítési mód beállítása
Az "upgrademode tulajdonság" konfigurációkat adhat a Microsoft.ServiceFabric/clusters erőforrás-definíció és a "clusterCodeVersion" értékre az alább látható módon támogatott fabric-verziók valamelyikét, és ezután helyezze üzembe a sablont. Az érvényes értékek az upgrademode "tulajdonság": "Kézi" vagy "Automatikus"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Egy fürtön, amelyek egy Resource Manager-sablon használatával manuális módra van beállítva egy új verziójára való frissítését.
A fürt manuális módban van, amikor egy új verzióra frissít, módosítsa a "clusterCodeVersion" egy támogatott verziójára, és telepítheti. A sablon központi telepítését, a Fabric frissítési megrúgni lekérdezi kezdődjön automatikusan. A fürt állapotházirendeket (csomópont állapotát, az kombinációja a fürtben futó összes alkalmazás) a frissítés során betartását.

Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva.  

Miután javította a problémákat, amelyek a visszaállítás eredményezett, a frissítés ismét kezdeményezése a következő ugyanazokat a lépéseket előtt kell.

## <a name="set-custom-health-polices-for-upgrades"></a>Egyéni állapot beállítása a frissítésekre szabályzatok
Megadhat egyéni irányelveinek fabric frissítése. Ha a fürt automatikus hálófrissítések értékre állította, akkor ezeket a szabályzatokat a rendszer alkalmazza a [– 1. fázisa az automatikus hálófrissítések](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Ha a manuális fabric-fürtöt verziófrissítések állított be, ezeket a szabályzatokat alkalmazva minden alkalommal, amikor egy új verzió indítása a rendszer a fabric frissítése a fürt elindítson választja. Ha nem bírálja felül a szabályzatokat, az alapértelmezett értékeket használják.

Adja meg az egyéni állapotházirendeket, vagy tekintse át a jelenlegi beállítások szerint a "fabric frissítése" panel a speciális frissítési beállítások kiválasztásával. Tekintse át a következő képen látható, hogyan. 

![Egyéni házirendek kezelése][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Minden környezet egy adott előfizetéshez tartozó összes elérhető verzió listázása
Futtassa a következő parancsot, és a kimenet ehhez hasonló kapja.

"supportExpiryUtc" arra utasítja a akkor, ha egy adott kiadás lejár vagy lejárt. A legfrissebb kiadás nem rendelkezik érvényes dátum -, amelynek értéke "9999-12-31T23:59:59.9999999", amely csak azt jelenti, hogy a lejárat dátumát még nem állították.

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
* Ismerje meg, hogyan szabhatja testre az egyes a [service fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md)
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md)
* Ismerje meg [alkalmazásfrissítések](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
