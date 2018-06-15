---
title: Biztonságos Windows biztonsági segítségével Windows rendszerű fürtre |} Microsoft Docs
description: Útmutató a Windows rendszerbiztonság használatával a Windows futtató önálló fürtön található csomópontok és ügyfél-csomópont biztonságának konfigurálása.
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
ms.openlocfilehash: 0f0df7883b25344560514491c08af3eadf872ffb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209137"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Biztonságos Windows önálló fürtben, a Windows biztonsági
A jogosulatlan hozzáférés elkerülése érdekében a Service Fabric-fürt, biztosítania kell a fürthöz. Biztonsági különösen fontos, amikor a fürt fut termelési számítási feladatokhoz. Ez a cikk ismerteti, hogyan csomópontok és ügyfél-csomópont biztonsági konfigurálása a Windows biztonsági használatával a *művelet* fájlt.  A folyamat megfelel-e a konfigurálás biztonsági lépés [létrehozása a Windows rendszert futtató önálló fürtön](service-fabric-cluster-creation-for-windows-server.md). Hogyan használja a Service Fabric a Windows biztonsági kapcsolatos további információkért lásd: [fürtök biztonsági forgatókönyveinek](service-fabric-cluster-security.md).

> [!NOTE]
> Vegye figyelembe a kijelölés-csomópontok biztonsági gondosan, mivel nincs Fürtfrissítés egy biztonsági választás a másikra. A biztonsági rendszer beállításának módosításához be kell, hogy a teljes fürtöt.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Csoportosan felügyelt szolgáltatásfiókot használó Windows biztonságának konfigurálása  
A minta *ClusterConfig.gMSA.Windows.MultiMachine.JSON* együtt letöltött konfigurációs fájlt a [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) önálló fürt csomag tartalmaz egy sablon konfigurálásához a Windows biztonsági használatával [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Beállítása *Windows* Windows biztonsági csomópontok kommunikáció engedélyezéséhez.  | 
| ServerCredentialType |Beállítása *Windows* Windows biztonsági az ügyfél-csomópontok kommunikáció engedélyezéséhez. |  
| WindowsIdentities |A fürt és az ügyfél identitások tartalmazza. |  
| ClustergMSAIdentity |Csomópont-csomópont biztonsági konfigurálja. Egy csoport felügyelt szolgáltatásfiók. |  
| ClusterSPN |Csoportosan felügyelt szolgáltatásfiók regisztrált egyszerű Szolgáltatásnevet|  
| ClientIdentities |Konfigurálja az ügyfél-csomópont biztonsági. Felhasználói fiókok tömb. | 
| Identitás |Adja hozzá a tartományi felhasználót, a tartomány\felhasználónév, az ügyfél-azonosító. |  
| IsAdmin |Adhatja meg, hogy a tartományi felhasználó rendelkezik-e a rendszergazda ügyfélelérési és hamis értéket, a felhasználó ügyfél-hozzáférési igaz értékre állítani. |  

[Csomópont biztonsági csomópont](service-fabric-cluster-security.md#node-to-node-security) úgy, hogy van-e konfigurálva **ClustergMSAIdentity** amikor a service fabric kell futtatni a csoportosan felügyelt szolgáltatásfiók alatt. Ahhoz, hogy a csomópontok közötti bizalmi kapcsolatok készítéséhez is el minden más kompatibilis. A két különböző módon lehet elvégezni: Adja meg a csoportosan felügyelt szolgáltatásfiók, amely a fürt összes csomópontján tartalmazza, vagy adja meg a tartományhoz gép, amely a fürt összes csomópontján tartalmazza. Határozottan javasoljuk a [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) módszert használja, különösen a nagyobb fürtökkel (több mint 10 csomópontok) vagy a fürtök, amelyek valószínűleg növekedhet és csökkenhet.  
Ez a megközelítés nem követeli meg, amelynek fürt rendszergazdák rendelkezik hozzáférési jogosultsággal hozzáadhat és eltávolíthat tagokat tartományi csoport létrehozását. Ezek a fiókok automatikus jelszókezelés is hasznosak. További információkért lásd: [Ismerkedés a csoportosan felügyelt szolgáltatásfiókok](http://technet.microsoft.com/library/jj128431.aspx).  
 
[A csomópont security ügyfél](service-fabric-cluster-security.md#client-to-node-security) segítségével konfigurálható: **ClientIdentities**. Ahhoz, hogy az ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozása, konfigurálnia kell a fürt tudni, hogy melyik ügyfél identitások megbízhatónak is. Ez kétféleképpen végezhető: Adja meg a tartományi felhasználók csoporthoz, amely képes csatlakozni, vagy adja meg a csomópont Tartományfelhasználók csatlakoztatható. Service Fabric két különböző hozzáférést vezérlő típusokat támogatja az ügyfelek csatlakoznak a Service Fabric-fürt: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi az a fürt a rendszergazdák korlátozhatják bizonyos típusú, különböző csoportok számára, így nagyobb biztonságot nyújt a fürt a fürt működését.  Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét. A hozzáférés-vezérlést további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).  
 
Az alábbi példa **biztonsági** szakasz csoportosan felügyelt szolgáltatásfiókot használó Windows biztonsági konfigurálja, és azt jelenti, hogy a gépek *ServiceFabric.clusterA.contoso.com* csoportosan felügyelt szolgáltatásfiók a fürt és a részétképezik. *CONTOSO\usera* felügyeleti ügyfél hozzáfér:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>A számítógép csoport használata Windows biztonságának konfigurálása  
Ez a modell is elavult. Az ajánljuk, hogy a csoportosan felügyelt szolgáltatásfiók használata részletes fent. A minta *ClusterConfig.Windows.MultiMachine.JSON* együtt letöltött konfigurációs fájlt a [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) önálló fürt csomag tartalmazza a sablon a Windows biztonsági beállításainak megadása.  Windows biztonsági konfigurálva van a **tulajdonságok** szakasz: 

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
| ClusterCredentialType |Beállítása *Windows* Windows biztonsági csomópontok kommunikáció engedélyezéséhez.  | 
| ServerCredentialType |Beállítása *Windows* Windows biztonsági az ügyfél-csomópontok kommunikáció engedélyezéséhez. |  
| WindowsIdentities |A fürt és az ügyfél identitások tartalmazza. |  
| ClusterIdentity |Használja a gép csoportnév domain\machinegroup,-csomópontok biztonsági konfigurálásához. |  
| ClientIdentities |Konfigurálja az ügyfél-csomópont biztonsági. Felhasználói fiókok tömb. |  
| Identitás |Adja hozzá a tartományi felhasználót, a tartomány\felhasználónév, az ügyfél-azonosító. |  
| IsAdmin |Adhatja meg, hogy a tartományi felhasználó rendelkezik-e a rendszergazda ügyfélelérési és hamis értéket, a felhasználó ügyfél-hozzáférési igaz értékre állítani. |  

[Csomópont biztonsági csomópont](service-fabric-cluster-security.md#node-to-node-security) konfigurálható beállítás **ClusterIdentity** Ha szeretné használni a számítógép csoport az Active Directory-tartományban. További információkért lásd: [létrehoz egy gép csoportot az Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Ügyfél-csomópont biztonsági](service-fabric-cluster-security.md#client-to-node-security) konfigurálható **ClientIdentities**. Ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozása, konfigurálnia kell a fürt tudni, hogy az ügyfél identitásokat tartalmaz, amelyek a fürt is megbízhat. Megbízhatósági kapcsolat két különböző módon is létrehozása:

- Adja meg a tartományi felhasználók csoporthoz, amely képes kapcsolódni.
- Adja meg a csomópont Tartományfelhasználók csatlakoztatható.

Service Fabric két különböző hozzáférést vezérlő típusokat támogatja az ügyfelek csatlakoznak a Service Fabric-fürt: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája korlátozhatják bizonyos típusú fürtműveletekben különböző csoportok számára, amely biztonságosabbá teszi a fürt.  Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.  

Az alábbi példa **biztonsági** szakasz konfigurálja a Windows biztonsági, azt jelenti, hogy a gépek *ServiceFabric/clusterA.contoso.com* a fürt részét képezik, és határozza meg, hogy *CONTOSO\usera* felügyeleti ügyfél hozzáfér:

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
> Service Fabric egy olyan tartományvezérlőre nem telepíthető. Győződjön meg arról, hogy nincs-e a tartományvezérlő IP-címét tartalmazza, a gép csoport használata esetén művelet, vagy csoportos felügyelt szolgáltatásfiók (gMSA).
>
>

## <a name="next-steps"></a>További lépések
A Windows biztonsági beállításainak megadása után a *művelet* fájlt, az alkalmazás Fürtlétrehozási folyamatának folytatásához [létrehozása a Windows rendszert futtató önálló fürtön](service-fabric-cluster-creation-for-windows-server.md).

További információ a hogyan-csomópontok biztonsági, az ügyfél-csomópont biztonsági és a szerepköralapú hozzáférés-vezérlés, lásd: [fürtök biztonsági forgatókönyveinek](service-fabric-cluster-security.md).

Lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md) példák a PowerShell vagy a FabricClient keresztül kapcsolódik.
