---
title: Windows-rendszerbiztonság használatával a Windows rendszerű fürt biztonságossá tétele |} A Microsoft Docs
description: Ismerje meg, egy önálló fürtön futó Windows-rendszerbiztonság használatával Windows-csomópontok és ügyfél-csomópont biztonságának konfigurálása.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 681ee66ca165ece170dd2a2ce2736cf55a44f1f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104080"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Különálló fürt védelme a Windows a Windows rendszerbiztonság használatával
Service Fabric-fürtön való illetéktelen hozzáférés megakadályozása, biztosítania kell a fürtöt. Biztonsági különösen fontos, amikor a fürt futtatása termelési számítási feladatokhoz. Ez a cikk ismerteti a csomópontok közötti és ügyfél-csomópont biztonság konfigurálása a Windows rendszerbiztonság használatával a *ClusterConfig.JSON* fájlt.  A folyamat megfelelő konfigurálás biztonsági lépésére [a Windows rendszert futtató önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md). Hogyan használja a Service Fabric a Windows biztonsági kapcsolatos további információkért lásd: [fürtök – biztonsági helyzetek](service-fabric-cluster-security.md).

> [!NOTE]
> Vegye figyelembe a csomópontok közötti biztonsági választott gondosan, mivel nem fürt frissítése egy biztonsági választhatja a másikra. A biztonsági beállításának módosításához kell újraépíteni a teljes fürtöt.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Csoportosan felügyelt szolgáltatásfiókot használó Windows biztonságának konfigurálása  
A minta *ClusterConfig.gMSA.Windows.MultiMachine.JSON* együtt letöltött konfigurációs fájlt a [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) önálló fürt csomag tartalmaz egy sablon konfigurálásához a Windows biztonsági használatával [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| ClusterCredentialType |Állítsa be *Windows* Windows biztonsági csomópontok közötti kommunikáció engedélyezéséhez.  | 
| ServerCredentialType |Állítsa be *Windows* Windows biztonsági kliens csomópont közötti kommunikáció engedélyezéséhez. |
| WindowsIdentities |A fürt és az ügyfél identitásokat tartalmaz. |
| ClustergMSAIdentity |Konfigurálja a csomópont a csomópont közötti biztonsághoz. A csoportosan felügyelt szolgáltatásfiók. |
| ClusterSPN |Csoportosan felügyelt szolgáltatásfiók egyszerű Szolgáltatásnevének regisztrált|
| ClientIdentities |Konfigurálja az ügyfél a csomópont közötti biztonsághoz. Ügyfél felhasználói fiókok tömbje. |
| Identitás |Adja hozzá a tartományi felhasználó, tartomány\felhasználónév ügyfél identitását. |
| IsAdmin |Adja meg, hogy a tartományi felhasználó rendszergazda ügyfél-hozzáférési és hamis értéket, az ügyfél hozzáférésének felhasználó rendelkezik-e (igaz) értékre. |

> [!NOTE]
> ClustergMSAIdentity érték nem szerepelhet a tartomány nevét, és csak a csoport felügyelt szolgáltatásfiók neve. I.E. "mysfgmsa" helyességét, és mindkét "tartomany / / mysfgmsa" vagy "mysfgmsa@mydomain" érvénytelenek; a tartomány a gazdagép által implicit módon.

[A csomópont security csomópont](service-fabric-cluster-security.md#node-to-node-security) úgy van konfigurálva **ClustergMSAIdentity** amikor szüksége van a service fabric csoportosan felügyelt szolgáltatásfiók alatt szeretné futtatni. Annak érdekében, hogy a csomópontok közötti bizalmi kapcsolatokat hozhat létre, is el kell tisztában egymással. Ez két különböző módon valósítható meg: Adja meg a csoportosan felügyelt szolgáltatásfiók, amely tartalmazza az összes csomóponton a fürtben, vagy adja meg a tartományi számítógép csoportot, amely tartalmazza az összes csomóponton a fürtben. Kifejezetten javasoljuk a [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) megközelítés, különösen a nagyobb fürtök (10-nél több csomópont) vagy a fürtök, amelyek valószínűleg növekedhet és csökkenhet.  
Ez a módszer nem igényel, amelynek fürt rendszergazdák kapott hozzáférési jogosultsága ahhoz, hozzáadhat és eltávolíthat tagokat tartományi csoport létrehozását. Ezek a fiókok automatikus jelszókezelés is hasznosak. További információkért lásd: [csoportosan felügyelt szolgáltatásfiókok – első lépések](https://technet.microsoft.com/library/jj128431.aspx).  
 
[A csomópont security ügyfél](service-fabric-cluster-security.md#client-to-node-security) használatára van konfigurálva, **ClientIdentities**. Annak érdekében, hogy az ügyfél és a fürt közötti megbízhatósági kapcsolatot hoz létre, konfigurálnia kell a fürt tudni, hogy melyik ügyfél, amely megbízható, identitásokat. Ezt kétféleképpen teheti meg: Adja meg a tartományi felhasználók csoporthoz, amely csatlakozni vagy adja meg a csomópont Tartományfelhasználók kapcsolódhatnak az. A Service Fabric támogatja a különböző hozzáférés-vezérlő kétféle Service Fabric-fürt csatlakozó ügyfelek számára: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi a fürt rendszergazdája a különböző csoportok számára, így több biztonságos a fürt a fürt működését bizonyos típusú való hozzáférés korlátozásához.  A rendszergazdák (beleértve az olvasási/írási képességeket) felügyeleti képességek teljes hozzáféréssel rendelkezik. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához. További információ a hozzáférés-vezérlés: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).  
 
Az alábbi példa **biztonsági** szakaszban konfigurálja a Windows biztonsági csoportosan felügyelt szolgáltatásfiókot használó, és azt jelenti, hogy a gépek *ServiceFabric.clusterA.contoso.com* csoportosan felügyelt szolgáltatásfiók a fürt és az adott részétképezik. *CONTOSO\usera* felügyeleti ügyfél hozzáfér:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurálja a Windows biztonsági gép csoport használatával  
Ez a modell hamarosan elavulttá válik. Az ajánljuk, hogy a csoportosan felügyelt szolgáltatásfiókot használja a fenti útmutató szerint. A minta *ClusterConfig.Windows.MultiMachine.JSON* együtt letöltött konfigurációs fájlt a [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) önálló fürt csomag tartalmazza a sablon a Windows biztonsági beállításainak megadása.  Windows biztonsági konfigurálva van a **tulajdonságok** szakaszban: 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| ClusterCredentialType |Állítsa be *Windows* Windows biztonsági csomópontok közötti kommunikáció engedélyezéséhez.  |
| ServerCredentialType |Állítsa be *Windows* Windows biztonsági kliens csomópont közötti kommunikáció engedélyezéséhez. |
| WindowsIdentities |A fürt és az ügyfél identitásokat tartalmaz. |
| ClusterIdentity |Egy gép csoportnevet domain\machinegroup, használja a csomópontok közötti biztonságának konfigurálása. |
| ClientIdentities |Konfigurálja az ügyfél a csomópont közötti biztonsághoz. Ügyfél felhasználói fiókok tömbje. |  
| Identitás |Adja hozzá a tartományi felhasználó, tartomány\felhasználónév ügyfél identitását. |  
| IsAdmin |Adja meg, hogy a tartományi felhasználó rendszergazda ügyfél-hozzáférési és hamis értéket, az ügyfél hozzáférésének felhasználó rendelkezik-e (igaz) értékre. |  

[A csomópont security csomópont](service-fabric-cluster-security.md#node-to-node-security) beállítás használatával konfigurálható **ClusterIdentity** Ha azt szeretné, a gép csoportot használja az Active Directory-tartományban. További információkért lásd: [hozzon létre egy gép csoportot az Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Ügyfél-csomópont biztonsági](service-fabric-cluster-security.md#client-to-node-security) használatával konfigurálható **ClientIdentities**. Ügyfél és a fürt közötti megbízhatósági kapcsolatot hoz létre, konfigurálnia kell a fürt tudni, hogy az ügyfél, amely a fürt megbízható identitásokat. Megbízhatósági kapcsolat két különböző módon is létrehozhat:

- Adja meg a tartományi felhasználók csoporthoz, amely csatlakozni tud.
- Adja meg a csomópont Tartományfelhasználók kapcsolódhatnak.

A Service Fabric támogatja a különböző hozzáférés-vezérlő kétféle Service Fabric-fürt csatlakozó ügyfelek számára: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája korlátozhatják bizonyos típusú különböző csoportjaihoz, felhasználók, a fürt működését, ami lehetővé teszi a fürt biztonságosabb.  A rendszergazdák (beleértve az olvasási/írási képességeket) felügyeleti képességek teljes hozzáféréssel rendelkezik. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához.  

Az alábbi példa **biztonsági** szakaszban konfigurálja a Windows biztonsági, azt jelenti, hogy a gépek *ServiceFabric/clusterA.contoso.com* a fürt részét képezik, és meghatározza, hogy *CONTOSO\usera* felügyeleti ügyfél hozzáfér:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> A Service Fabric tartományvezérlőn nem kerül sor. Ellenőrizze, hogy nincs-e a tartományvezérlő IP-címét tartalmazza, a gép csoport használata esetén ClusterConfig.json, vagy csoportosan felügyelt szolgáltatásfiók (gMSA).
>
>

## <a name="next-steps"></a>További lépések
A Windows biztonsági konfigurálása után a *ClusterConfig.JSON* fájlt, a Fürtlétrehozási folyamatának folytatásához [a Windows rendszert futtató önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md).

Hogyan-csomópontok biztonsági, az ügyfél a csomópont közötti biztonsághoz és a szerepköralapú hozzáférés-vezérlés, lásd: További információ [fürtök – biztonsági helyzetek](service-fabric-cluster-security.md).

Lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md) példák csatlakoztathatók a PowerShell vagy a FabricClient használatával.
