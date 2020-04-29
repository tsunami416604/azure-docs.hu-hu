---
title: Windows rendszerű fürt biztonságossá tétele Windows-Biztonság használatával
description: Megtudhatja, hogyan konfigurálhatja a csomópontok közötti és az ügyfél és a csomópont közötti biztonságot a Windows rendszeren futó önálló fürtön a Windows biztonsági szolgáltatással.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774441"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Önálló fürt biztonságossá tétele Windows rendszeren a Windows biztonsági szolgáltatással
A Service Fabric fürthöz való jogosulatlan hozzáférés megakadályozása érdekében védenie kell a fürtöt. A biztonság különösen akkor fontos, ha a fürt éles számítási feladatokat futtat. Ez a cikk azt ismerteti, hogyan konfigurálható a csomópontok közötti és az ügyfél és a csomópont közötti biztonság a Windows Biztonság használatával a *ClusterConfig. JSON* fájlban.  A folyamat megfelel a [Windows rendszeren futó önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)biztonsági lépéseként. További információ arról, hogy a Service Fabric hogyan használja a Windows-biztonságot: a [fürtök biztonsági forgatókönyvei](service-fabric-cluster-security.md).

> [!NOTE]
> Érdemes figyelembe vennie a csomópontok közötti biztonság kiválasztását, mivel nincs olyan fürt, amely az egyik biztonsági megoldásból a másikra van frissítve. A biztonsági kijelölés módosításához újra kell építenie a teljes fürtöt.
>
>

## <a name="configure-windows-security-using-gmsa"></a>A Windows Biztonság konfigurálása a gMSA használatával  
A minta *ClusterConfig. gMSA. Windows. MultiMachine. JSON* konfigurációs fájl letölthető a [Microsoft. Azure. ServiceFabric.\< windowsserver fájllal. ](https://go.microsoft.com/fwlink/?LinkId=730690)a (z)>. zip önálló fürtjének verziója tartalmaz egy sablont a Windows biztonsági beállításainak a [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx)használatával történő konfigurálásához:  

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
| ClusterCredentialType |Állítsa a *Windows* rendszerre, hogy engedélyezze a Windows Biztonság csomópont-csomópont típusú kommunikációt.  | 
| ServerCredentialType |Állítsa a *Windows* -ra lehetőséget, hogy engedélyezze a Windows biztonságot az ügyfél-csomópont kommunikációhoz. |
| WindowsIdentities |A fürt és az ügyfél identitását tartalmazza. |
| ClustergMSAIdentity |A csomópontok közötti biztonságot konfigurálja. Csoportosan felügyelt szolgáltatásfiók. |
| ClusterSPN |Regisztrált SPN a gMSA-fiókhoz|
| ClientIdentities |Az ügyfél és a csomópont közötti biztonság konfigurálása. Az ügyfél felhasználói fiókjainak tömbje. |
| Identitás |Adja hozzá a tartományi felhasználót (tartomány \ Felhasználónév) az ügyfél identitásához. |
| IsAdmin |Állítsa igaz értékre, ha azt szeretné megadni, hogy a tartományi felhasználó rendszergazdai ügyfél-hozzáféréssel vagy FALSE értékkel rendelkezik a felhasználói ügyfelek hozzáféréséhez. |

> [!NOTE]
> A ClustergMSAIdentity értékének a következő formátumúnak kell lennie: "mysfgmsa@mydomain".

A csomópontok közötti [Biztonság](service-fabric-cluster-security.md#node-to-node-security) a **ClustergMSAIdentity** beállításával állítható be, ha a Service fabricnek a gMSA alatt kell futnia. A csomópontok közötti megbízhatósági kapcsolatok létrehozásához tudniuk kell egymással kapcsolatban. Ez két különböző módon valósítható meg: megadhatja a fürtben lévő összes csomópontot tartalmazó csoportosan felügyelt szolgáltatásfiókot, vagy megadhatja a fürtben lévő összes csomópontot tartalmazó tartományi számítógép csoportot is. Javasoljuk, hogy használja a [csoportosan felügyelt szolgáltatásfiók (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) megközelítését, különösen a nagyobb fürtök (több mint 10 csomópont) vagy a valószínűleg növekedni vagy zsugorodó fürtök esetében.  
Ehhez a megközelítéshez nincs szükség olyan tartományi csoport létrehozására, amelyhez a fürt rendszergazdája hozzáférési jogokat kapott a tagok hozzáadásához és eltávolításához. Ezek a fiókok az automatikus jelszavas felügyelethez is hasznosak. További információ: [első lépések csoportosan felügyelt szolgáltatásfiókok](https://technet.microsoft.com/library/jj128431.aspx).  
 
Az [ügyfél és a csomópont közötti biztonság](service-fabric-cluster-security.md#client-to-node-security) a **ClientIdentities**használatával van konfigurálva. Az ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozásához konfigurálnia kell a fürtöt, hogy megtudja, melyik ügyfél-identitások bízhatnak meg. Ezt kétféleképpen teheti meg: megadhatja, hogy mely felhasználók csatlakozhatnak a hálózathoz, vagy megadhatják azokat a tartományi csomópontokat, akik csatlakozhatnak. Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz csatlakozó ügyfelekhez: rendszergazda és felhasználó. A hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája korlátozza a hozzáférést bizonyos típusú fürtökhöz a különböző felhasználói csoportok számára, így a fürt biztonságosabbá tehető.  A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az írási/olvasási képességeket is). A felhasználók alapértelmezés szerint csak olvasási jogosultságot biztosítanak a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és szolgáltatások feloldásának lehetőségét. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlése](service-fabric-cluster-security-roles.md).  
 
A következő példa **biztonsági** szakasz a Windows-biztonságot konfigurálja a gMSA használatával, és megadja, hogy a *ServiceFabric.clusterA.contoso.com* gMSA lévő gépek a fürt részét képezik, és hogy a *CONTOSO\usera* rendszergazdai ügyfél-hozzáféréssel rendelkezik:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>A Windows biztonsági beállítások konfigurálása gépi csoport használatával  
Ez a modell elavult. Javasoljuk, hogy a fentiekben ismertetett gMSA használja. A minta *ClusterConfig. Windows. MultiMachine. JSON* konfigurációs fájl letölthető a [Microsoft. Azure. ServiceFabric. windowsserver fájllal.\< a>. zip](https://go.microsoft.com/fwlink/?LinkId=730690) önálló fürtjének verziója tartalmaz egy sablont a Windows biztonságának konfigurálásához.  A Windows biztonsági beállításai a **Tulajdonságok** szakaszban vannak konfigurálva: 

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
| ClusterCredentialType |Állítsa a *Windows* rendszerre, hogy engedélyezze a Windows Biztonság csomópont-csomópont típusú kommunikációt.  |
| ServerCredentialType |Állítsa a *Windows* -ra lehetőséget, hogy engedélyezze a Windows biztonságot az ügyfél-csomópont kommunikációhoz. |
| WindowsIdentities |A fürt és az ügyfél identitását tartalmazza. |
| ClusterIdentity |A csomópontok közötti biztonság konfigurálásához használjon domain\machinegroup nevet. |
| ClientIdentities |Az ügyfél és a csomópont közötti biztonság konfigurálása. Az ügyfél felhasználói fiókjainak tömbje. |  
| Identitás |Adja hozzá a tartományi felhasználót (tartomány \ Felhasználónév) az ügyfél identitásához. |  
| IsAdmin |Állítsa igaz értékre, ha azt szeretné megadni, hogy a tartományi felhasználó rendszergazdai ügyfél-hozzáféréssel vagy FALSE értékkel rendelkezik a felhasználói ügyfelek hozzáféréséhez. |  

A csomópontok közötti [biztonsági](service-fabric-cluster-security.md#node-to-node-security) beállításokat a **ClusterIdentity** használatával állíthatja be, ha egy Active Directory-tartományon belül szeretne gépi csoportot használni. További információ: [Machine Group létrehozása a Active Directoryban](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

Az [ügyfél és a csomópont közötti biztonság](service-fabric-cluster-security.md#client-to-node-security) a **ClientIdentities**használatával konfigurálható. Az ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozásához úgy kell beállítania a fürtöt, hogy megismerje a fürt által megbízhatónak ítélt ügyfél-identitásokat. Két különböző módon hozhat létre megbízhatósági kapcsolatot:

- Itt adhatja meg azokat a tartományi csoportokat, akik csatlakozhatnak.
- Válassza ki azokat a tartományi csomópont-felhasználókat, akik csatlakozhatnak.

Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz csatlakozó ügyfelekhez: rendszergazda és felhasználó. A hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája korlátozza a hozzáférést bizonyos típusú fürtökhöz a különböző felhasználói csoportok esetében, ami biztonságosabbá teszi a fürtöt.  A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az írási/olvasási képességeket is). A felhasználók alapértelmezés szerint csak olvasási jogosultságot biztosítanak a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és szolgáltatások feloldásának lehetőségét.  

A következő példa **biztonsági** szakasz a Windows biztonsági beállításokat konfigurálja, és megadja, hogy a *ServiceFabric/clustera. contoso. com* -ban lévő gépek a fürt részét képezik, és megadja, hogy a *CONTOSO\usera* rendszergazdai jogosultsággal rendelkezik:

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
> Service Fabric nem telepíthető tartományvezérlőre. Győződjön meg arról, hogy a ClusterConfig. JSON nem tartalmazza a tartományvezérlő IP-címét egy gépi csoport vagy csoportosan felügyelt szolgáltatásfiók (gMSA) használata esetén.
>
>

## <a name="next-steps"></a>További lépések
Miután konfigurálta a Windows-biztonságot a *ClusterConfig. JSON* fájlban, folytassa a fürt létrehozásának folyamatát a [Windows rendszeren futó önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)című témakörben.

További információ a csomópontok közötti biztonságról, az ügyfelek és a csomópontok biztonságáról, valamint a szerepköralapú hozzáférés-vezérlésről: [fürtök biztonsági forgatókönyvei](service-fabric-cluster-security.md).

Lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md) példák a PowerShell vagy a FabricClient használatával történő csatlakozásra.
