---
title: Windows rendszeren futó fürt biztonságossá tétele a Windows biztonsága segítségével
description: Megtudhatja, hogy miként konfigurálható a csomópontok és az ügyfél-csomópont biztonság a Windows rendszeren futó önálló fürtön a Windows biztonsága segítségével.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774441"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Önálló fürt biztonságossá tétele a Windows rendszeren a Windows biztonsága segítségével
A Service Fabric-fürthöz való jogosulatlan hozzáférés megakadályozása érdekében biztosítania kell a fürtöt. A biztonság különösen fontos, ha a fürt éles számítási feladatokat futtat. Ez a cikk azt ismerteti, hogy miként konfigurálható a csomópont-csomópont és az ügyfél-csomópont biztonság a *Fürtconfig.JSON* fájlWindows-biztonságával.  A folyamat megfelel a Windows [rendszeren futó önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)biztonsági lépésének. Arról, hogy a Service Fabric hogyan használja a Windows biztonságát, [a Fürtbiztonsági forgatókönyvek](service-fabric-cluster-security.md)című témakörben talál további információt.

> [!NOTE]
> Gondosan vegye figyelembe a csomópontok között történő biztonság kiválasztását, mivel nincs fürtfrissítés egyik biztonsági beállításról a másikra. A biztonsági kijelölés módosításához újra kell építenie a teljes fürtöt.
>
>

## <a name="configure-windows-security-using-gmsa"></a>A Windows biztonságának konfigurálása a gMSA használatával  
A *Minta ClusterConfig.gMSA.Windows.MultiMachine.JSON konfigurációs* fájl a [Microsoft.Azure.ServiceFabric.WindowsServer fájlból letöltött.\< a>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) verzió önálló fürtcsomag sablont tartalmaz a Windows biztonságának [csoportkezelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx)használatával történő konfigurálásához:  

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
| ClusterCredentialType típus |Állítsa *a Windows* rendszerre a Windows biztonságának engedélyezéséhez a csomópont-csomópont kommunikációhoz.  | 
| ServerCredentialType |Állítsa a *Windows* rendszerre az ügyfél-csomópont kommunikáció Windows-biztonságának engedélyezéséhez. |
| WindowsIdentitások |A fürt- és ügyfélidentitásokat tartalmazza. |
| ClustergMSAIdentity |A csomópontok és csomópontok biztonságának konfigurálása. Csoport kezelt szolgáltatásfiók. |
| FürtSPN |Regisztrált SPN a gMSA-fiókhoz|
| Ügyfélidentitások |Az ügyfél-csomópont biztonság konfigurálása. Ügyfélfelhasználói fiókok tömbje. |
| Identitás |Adja hozzá a tartományfelhasználót(tartomány\felhasználónév) az ügyfélidentitáshoz. |
| IsAdmin |Értéke igaz, hogy a tartományi felhasználó rendszergazdai ügyfél-hozzáféréssel vagy hamis felhasználói ügyfélhozzáféréssel rendelkezik-e. |

> [!NOTE]
> A ClustergMSAIdentity értéknekmysfgmsa@mydomain" formátumúnak kell lennie.

[Csomópont-csomópont biztonság](service-fabric-cluster-security.md#node-to-node-security) konfigurálva a **FürtgMSAIdentity** beállítása, ha a szolgáltatás háló kell futtatni a gMSA alatt. A csomópontok közötti bizalmi kapcsolatok kiépítéséhez fel kell őket ismerni. Ez két különböző módon valósítható meg: Adja meg a csoport felügyelt szolgáltatásfiókot, amely tartalmazza a fürt összes csomópontját, vagy adja meg azt a tartományi számítógépcsoportot, amely a fürt összes csomópontját tartalmazza. Javasoljuk, hogy a [csoport felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) megközelítést használja, különösen nagyobb (10 csomópontnál nagyobb) fürtök esetén, illetve olyan fürtök esetében, amelyek valószínűleg növekedni vagy zsugorodni fognak.  
Ez a megközelítés nem követeli meg olyan tartománycsoport létrehozását, amelyhez a fürtrendszergazdák hozzáférési jogokat kaptak a tagok hozzáadásához és eltávolításához. Ezek a fiókok az automatikus jelszókezeléshez is hasznosak. További információt a [Csoportkezelt szolgáltatásfiókok – első lépések című témakörben talál.](https://technet.microsoft.com/library/jj128431.aspx)  
 
[Az ügyfél-csomópont biztonság](service-fabric-cluster-security.md#client-to-node-security) **ügyfélidentitások**használatával van konfigurálva. Az ügyfél és a fürt közötti bizalmi kapcsolat létrehozásához konfigurálnia kell a fürtöt, hogy tudja, mely ügyfélidentitásokban bízhat meg. Ez kétféleképpen végezhető el: Adja meg azokat a tartománycsoport-felhasználókat, akik csatlakozhatnak vagy megadhatják a csatlakozható tartománycsomópont-felhasználókat. A Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz kapcsolódó ügyfelek számára: a rendszergazdát és a felhasználót. A hozzáférés-vezérlés lehetővé teszi a fürtrendszergazdának, hogy korlátozza a hozzáférést bizonyos típusú fürtműveletekhez a felhasználók különböző csoportjai számára, így a fürt biztonságosabbá teszi a fürtöt.  A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az olvasási/írási képességeket is). A felhasználók alapértelmezés szerint csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez), és képesek az alkalmazások és szolgáltatások feloldására. A hozzáférés-vezérlésről a [Service Fabric-ügyfelek szerepköralapú hozzáférés-vezérlése](service-fabric-cluster-security-roles.md)című témakörben talál további információt.  
 
A következő biztonsági példa **a** Windows biztonságát gMSA használatával konfigurálja, és azt adja meg, hogy a ServiceFabric.clusterA.contoso.com gMSA-ban lévő gépek a fürt részét képezik, és hogy a CONTOSO\usera rendszergazdai ügyfélhozzáféréssel rendelkezik:The following example security using gMSA and specifies *that the* machines in ServiceFabric.clusterA.contoso.com gMSA are part of the cluster and that *CONTOSO\usera* has admin client access:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>A Windows biztonságának konfigurálása gépcsoport használatával  
Ez a modell elavult. Az ajánlás a fent részletezett gMSA használatát javasolja. A Microsoft.Azure.ServiceFabric.WindowsServer fájlból letöltött *ClusterConfig.Windows.MultiMachine.JSON* konfigurációs [fájl.\< a>.zip verzió](https://go.microsoft.com/fwlink/?LinkId=730690) önálló fürtcsomag a Windows biztonságának konfigurálásához sablont tartalmaz.  A Windows biztonsága a **Tulajdonságok** szakaszban van konfigurálva: 

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
| ClusterCredentialType típus |Állítsa *a Windows* rendszerre a Windows biztonságának engedélyezéséhez a csomópont-csomópont kommunikációhoz.  |
| ServerCredentialType |Állítsa a *Windows* rendszerre az ügyfél-csomópont kommunikáció Windows-biztonságának engedélyezéséhez. |
| WindowsIdentitások |A fürt- és ügyfélidentitásokat tartalmazza. |
| Fürtidentitás |A csomópont-csomópont biztonság konfigurálásához használjon számítógépcsoportnevet (tartomány\számítógépcsoport). |
| Ügyfélidentitások |Az ügyfél-csomópont biztonság konfigurálása. Ügyfélfelhasználói fiókok tömbje. |  
| Identitás |Adja hozzá a tartományfelhasználót(tartomány\felhasználónév) az ügyfélidentitáshoz. |  
| IsAdmin |Értéke igaz, hogy a tartományi felhasználó rendszergazdai ügyfél-hozzáféréssel vagy hamis felhasználói ügyfélhozzáféréssel rendelkezik-e. |  

[A csomópont-csomópont biztonság](service-fabric-cluster-security.md#node-to-node-security) a **ClusterIdentity** beállítással van konfigurálva, ha egy Active Directory tartományon belüli gépcsoportot szeretne használni. További információt a [Számítógép-csoport létrehozása az Active Directoryban című témakörben talál.](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)

[Az ügyfél-csomópont biztonság](service-fabric-cluster-security.md#client-to-node-security) **ügyfélidentitások**használatával van konfigurálva. Az ügyfél és a fürt közötti bizalmi kapcsolat létrehozásához konfigurálnia kell a fürtöt, hogy ismerje a fürt által megbízhatónak ismerte azokat az ügyfélidentitásokat. A bizalmi bizalmat kétféleképpen hozhatja létre:

- Adja meg a tartománycsoport azon felhasználóit, akik csatlakozhatnak.
- Adja meg a csatlakozható tartománycsomópont-felhasználókat.

A Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz kapcsolódó ügyfelek számára: a rendszergazdát és a felhasználót. A hozzáférés-vezérlés lehetővé teszi a fürtrendszergazdának, hogy korlátozza a hozzáférést bizonyos típusú fürtműveletekhez a különböző felhasználói csoportok számára, ami biztonságosabbá teszi a fürtöt.  A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az olvasási/írási képességeket is). A felhasználók alapértelmezés szerint csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez), és képesek az alkalmazások és szolgáltatások feloldására.  

A következő biztonsági példa a Windows biztonságát konfigurálja, azt adja meg, hogy a ServiceFabric/clusterA.contoso.com webhely gépei a fürt részét képezik, és megadja, hogy a CONTOSO\usera rendszergazdai ügyfélhozzáféréssel rendelkezik:The following example **security** section configures Windows security, specifies that the machines in *ServiceFabric/clusterA.contoso.com* are part of the cluster, and specifies that *CONTOSO\usera* has admin client access:

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
> A Service Fabric nem telepíthető tartományvezérlőre. Győződjön meg arról, hogy a ClusterConfig.json nem tartalmazza a tartományvezérlő IP-címét, ha számítógépcsoportot vagy csoportfelügyelt szolgáltatásfiókot (gMSA) használ.
>
>

## <a name="next-steps"></a>További lépések
Miután konfigurálta a Windows biztonságát a *ClusterConfig.JSON* fájlban, folytassa a fürt létrehozási folyamatát [a Windows rendszeren futó önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)című témakörben.

A csomópont-csomópont biztonságról, az ügyfél-csomópont biztonságról és a szerepköralapú hozzáférés-vezérlésről a [Fürtbiztonsági forgatókönyvek](service-fabric-cluster-security.md)című témakörben talál további információt.

A PowerShell vagy a FabricClient használatával történő csatlakozás példákat a [Csatlakozás biztonságos fürthöz( Csatlakozás biztonságos fürthöz)](service-fabric-connect-to-secure-cluster.md) témakörben talál.
