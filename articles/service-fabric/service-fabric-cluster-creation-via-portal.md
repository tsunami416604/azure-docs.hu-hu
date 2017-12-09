---
title: "Service Fabric-fürt létrehozása az Azure portálon |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be az Azure-ban az Azure portál és az Azure Key Vault biztonságos Service Fabric-fürt."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: 6ddadad6f5697fed006e3f938ef3c3faedb6a354
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>A Service Fabric-fürt létrehozása az Azure-ban az Azure-portálon
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ez a cikk részletesen ismerteti, amely végigvezeti a lépéseken, hogyan kell beállítani az Azure-ban az Azure-portálon biztonságos Service Fabric-fürt. Ez az útmutató végigvezeti az alábbi lépéseket:

* Key Vault beállítása a fürt biztonsági kulcsok kezeléséhez.
* Védett fürt létrehozása az Azure-ban az Azure portálon keresztül.
* Rendszergazdák tanúsítványok segítségével hitelesíti.

> [!NOTE]
> A speciális biztonsági beállítások, például a felhasználói hitelesítés az Azure Active Directory és az alkalmazás biztonsági tanúsítványok beállítását [Azure Resource Manager használatával a fürt létrehozása][create-cluster-arm].
> 
> 

A biztonságos fürtre egy fürt, amely megakadályozza a jogosulatlan műveleteket, beleértve telepítése, frissítése és törlése, alkalmazások, szolgáltatások és hogy milyen adatokat tartalmaznak. Egy nem biztonságos fürt olyan fürt, hogy bárki bármikor csatlakozhat, és felügyeleti műveletek. Bár lehetséges egy nem biztonságos fürtök létrehozásához, **erősen ajánlott a biztonságos fürtök**. Egy nem biztonságos fürt **később nem védhető** -léteznie kell egy új fürtöt.

A koncepció ismertetése érdekében azonosak, biztonságos fürtök létrehozására akár a fürtök Windows vagy Linux fájlkiszolgálófürtökkel. További információk és segítő parancsfájlok biztonságos Linux-fürtök létrehozásához, ellenőrizze a [biztonságos fürtök létrehozására](service-fabric-cluster-creation-via-arm.md). A paraméterek megadott segítő parancsfájl révén is bemeneti közvetlenül be a portálra, a szakaszban leírt módon [fürt létrehozása az Azure portálon](#create-cluster-portal).

## <a name="configure-key-vault"></a>Key Vault beállítása 
### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ez az útmutató használ [Azure PowerShell][azure-powershell]. Ha egy új PowerShell-munkamenet indítása, jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését az Azure parancsok végrehajtása előtt.

Jelentkezzen be az azure-fiókjával:

```powershell
Login-AzureRmAccount
```

Jelölje ki az előfizetését:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="set-up-key-vault"></a>A Key Vault beállítása
Ez a kijelző az útmutató végigvezeti a kulcstároló, és a Service Fabric-alkalmazások számára az Azure Service Fabric-fürtök létrehozása. A Key Vault teljes körű, tekintse meg a [Key Vault – első lépések útmutató][key-vault-get-started].

A Service Fabric fürt védelmét X.509-tanúsítványokat használ. Az Azure Key Vault segítségével kezelheti az Azure Service Fabric-fürtök tanúsítványait. A fürt telepítésekor az Azure-ban, a Service Fabric-fürtök létrehozásáért felelős az Azure erőforrás-szolgáltató kéri le a tanúsítványokat a Key Vault, és telepíti azokat a virtuális gépek fürtön.

A következő ábra szemlélteti a Key Vault, a Service Fabric-fürt és a Key Vault tárolódnak, amikor létrehozza a fürt tanúsítványokat használ az Azure erőforrás-szolgáltató közötti kapcsolat:

![Tanúsítvány telepítése][cluster-security-cert-installation]

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az első lépés, hogy kimondottan a Key Vault hozzon létre egy új erőforráscsoportot. Key Vault üzembe a saját erőforráscsoport használata javasolt, hogy a számítási és tárolási erőforráscsoportok – például az erőforráscsoport, amelyen a Service Fabric-fürt - eltávolíthatja a kulcsok és titkos elvesztése nélkül. Az erőforráscsoport, amelynek a Key Vault a fürt által használt ugyanabban a régióban kell lennie.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

#### <a name="create-key-vault"></a>Kulcstartó létrehozása
A kulcstároló létrehozása az új erőforráscsoportot. A Key Vault **engedélyezni kell a központi telepítés** tanúsítványok beszerzése és telepítése a fürtcsomópontokon a Service Fabric erőforrás-szolgáltató engedélyezéséhez:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Ha egy meglévő kulcstároló, a központi telepítés Azure parancssori felület használatával engedélyezheti azt:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


### <a name="add-certificates-to-key-vault"></a>Tanúsítványok hozzáadása a Key Vault
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. További információ a tanúsítványok használatának módját a Service Fabric: [Service Fabric-fürt biztonsági forgatókönyvek][service-fabric-cluster-security].

#### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Ez a tanúsítvány szükséges biztosításához a fürt és a jogosulatlan hozzáférés elkerülése érdekében azt. Fürt biztonsági néhány módon tartalmazza:

* **Fürt hitelesítési:** hitelesíti a fürt összevonási csomópontok kommunikációt. Csak olyan csomópontot, amely bizonyítja, ezzel a tanúsítvánnyal az identitásukat csatlakozhat a fürthöz.
* **Kiszolgálóhitelesítés:** hitelesíti a felügyeleti ügyfél, a fürt felügyeleti végpontokat, így a felügyeleti ügyfél tudja azt a valódi fürthöz van szó. Ez a tanúsítvány is biztosít SSL a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül.

Ezeket a célokat szolgál, hogy a tanúsítvány a következő követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal.
* A kulcscseréhez használt, a személyes információcsere (.pfx) fájl exportálható léteznie kell a tanúsítványt.
* A tanúsítvány tulajdonosának nevét meg kell egyeznie a Service Fabric-fürt eléréséhez használt tartomány. Ez feltétele SSL biztosít a fürt HTTPS felügyeleti végpontok és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a `.cloudapp.azure.com` tartomány. Szerezzen be egy egyéni tartománynevet a fürt számára. Ha tanúsítványt kérhet egy hitelesítésszolgáltató a tanúsítvány tulajdonosának nevét meg kell egyeznie a fürt használja ezeket az egyéni tartománynév.

#### <a name="client-authentication-certificates"></a>Ügyfél-hitelesítési tanúsítványok
További ügyfél-tanúsítványok fürt felügyeleti feladatok rendszergazdák hitelesítéséhez. A Service Fabric két olyan hozzáférési szintje van: **admin** és **írásvédett felhasználó**. Legalább a rendszergazdai hozzáférést a rendszer egy tanúsítványt kell használni. A további felhasználói szintű hozzáférés érdekében egy külön tanúsítványt kell megadni. A hozzáférés szerepkörök további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric ügyfelek][service-fabric-cluster-security-roles].

Nem kell együttműködni a Service Fabric Key Vault ügyfél-hitelesítési tanúsítványok feltöltése. Meg kell adni a felhasználók számára, akik jogosultak a fürtkezeléshez csak kell ezeket a tanúsítványokat. 

> [!NOTE]
> Az Azure Active Directory használatával hitelesíti az ügyfeleket a fürtfelügyeleti műveleteket ajánlott módja. Az Azure Active Directoryt, le kell [hozzon létre egy fürtöt, Azure Resource Manager használatával][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványokat is telepíthető egy fürt biztonsági okokból. Az fürt létrehozása előtt fontolja meg az alkalmazás biztonsági kell telepíteni a csomópontokat, például a tanúsítványt igénylő forgatókönyvek:

* Az alkalmazás konfigurációs értékeit titkosítását és visszafejtését
* Replikáció során az adatok csomópontok közötti titkosítása 

Tanúsítványok nem állítható be, amikor az Azure portálon keresztül egy fürtöt hoz létre. Tanúsítványok konfigurálása a fürt telepítéskor, kell [hozzon létre egy fürtöt, Azure Resource Manager használatával][create-cluster-arm]. Azt is megteheti alkalmazástanúsítványokra a fürt létrehozása után.

#### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Az Azure erőforrás-szolgáltató használt tanúsítványok formázás
Titkos kulcs fájlok (.pfx) is felvehető és használható közvetlenül a Key Vault keresztül. Azonban az Azure erőforrás-szolgáltató szükséges kulcsok történő tárolását egy különös JSON formátumban, amely tartalmazza a .pfx, mint a base-64 kódolású karakterlánc és a titkos kulcs jelszava. Ezen követelmények teljesítése kulcsok kell helyezni egy JSON-karakterláncban, és tárolja majd *titkok* a Key Vault.

Ez a folyamat megkönnyítése egy PowerShell-modul van [elérhető a Githubon][service-fabric-rp-helpers]. Kövesse az alábbi lépéseket modulját:

1. Töltse le a tárházban teljes tartalmát egy helyi könyvtárba. 
2. Importálja a modult a PowerShell-ablakban:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

A `Invoke-AddCertToKeyVault` parancsot a PowerShell modul automatikusan a tanúsítvány titkos kulcsa alakítja JSON karakterláncnak és feltölti azt kulcstároló. A fürt tanúsítványt és a további tanúsítványok hozzáadása a Key Vault használja azt. Ismételje meg ezt a lépést minden további tanúsítványokat, a fürtön telepíteni szeretné.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Ezek azok a Key Vault szükséges előfeltételek maradéktalanul konfigurálása a Service Fabric fürt Resource Manager-sablon, amely telepíti csomópont hitelesítés, a felügyeleti végpontok közötti védelem és a hitelesítési tanúsítványokat, és minden további biztonsági szolgáltatások, X.509-tanúsítványokat használ. Ezen a ponton most már rendelkeznie kell a következő beállítása az Azure-ban:

* Key Vault erőforráscsoport
  * Key Vault
    * Fürt kiszolgálói hitelesítési tanúsítvány

< /a "létrehozása, fürt, portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Fürt létrehozása az Azure-portálon
### <a name="search-for-the-service-fabric-cluster-resource"></a>Keresse meg a Service Fabric-fürt erőforrás
![Keresse meg a Service Fabric fürt sablont az Azure portálon.][SearchforServiceFabricClusterTemplate]

1. Jelentkezzen be az [Azure Portalra][azure-portal].
2. Kattintson a **új** új erőforrás sablon hozzáadását. Keresse meg a Service Fabric-fürt-sablon a **piactér** alatt **mindent**.
3. Válassza ki **Service Fabric-fürt** a listából.
4. Keresse meg a **Service Fabric-fürt** panelen kattintson a **létrehozása**,
5. A **létrehozása a Service Fabric-fürt** panel rendelkezik a következő négy lépést.

#### <a name="1-basics"></a>1. Alapvető beállítások
![Képernyőfelvétel az új erőforráscsoport létrehozásához.][CreateRG]

Az alapvető beállítások panel szükség alapszintű adatainak megadása a fürthöz.

1. Adja meg a fürt nevét.
2. Adjon meg egy **felhasználónév** és **jelszó** a virtuális gépekhez távoli asztal.
3. Ügyeljen arra, hogy válassza ki a **előfizetés** , amelyet a fürt üzembe helyezni, különösen akkor, ha több előfizetéssel rendelkezik.
4. Hozzon létre egy **új erőforráscsoport**. A legcélszerűbb adjon neki a neve megegyezik a fürt, mivel segíti a keresés, azokat később, különösen akkor, ha módosítja a központi telepítés, vagy törölje a fürtöt, kívánt.
   
   > [!NOTE]
   > Eldöntheti, hogy egy meglévő erőforráscsoportot használni, de ajánlott hozzon létre egy új erőforráscsoportot. Ez megkönnyíti az fürtökben, amelyek nem kell törölni.
   > 
   > 
5. Válassza ki a **régió** a kívánja a fürt létrehozásához. A Key Vault szerepel ugyanabban a régióban kell használnia.

#### <a name="2-cluster-configuration"></a>2. Fürtkonfiguráció
![Csomópont-típus létrehozása][CreateNodeType]

Konfigurálja a fürtcsomópontokat. Csomóponttípusok a Virtuálisgép-méretek, a virtuális gépek számát és azok tulajdonságait határozza meg. A fürt több csomóponttípus is rendelkezik, de az elsődleges csomóponttípusok (az első egy Ön által a portál), ez a csomópont típusa ahol kerülnek, a Service Fabric rendszerszolgáltatások kell rendelkeznie legalább öt virtuális gépeket. Ne konfiguráljon **elhelyezési tulajdonságok** , mert a rendszer automatikusan hozzáadja egy alapértelmezett elhelyezési "NodeTypeName" tulajdonságát.

> [!NOTE]
> Egy általános forgatókönyv több csomópont esetében az előtér-szolgáltatás és a háttér-szolgáltatás tartalmazó alkalmazást. El szeretné helyezni az előtér-szolgáltatás a kisebb rendelkező virtuális gépek (VM-méretek D2 beállításaihoz hasonlóan) portok, nyissa meg az internethez, de el szeretné helyezni a háttér-szolgáltatás a nagyobb virtuális gépek (VM-méretek például D4 D6, D15 és így tovább) internetre portot.
> 
> 

1. Adjon nevet a csomóponttípus (1 – 12 csak betűket és számokat tartalmazó karakter).
2. A minimális **mérete** az elsődleges csomóponthoz tartozó virtuális gépek típus által vezérelt a **tartóssági** réteg úgy dönt, hogy a fürt számára. A tartóssági szint alapértelmezés szerint bronz. A durability további információkért lásd: [kiválasztása a Service Fabric-fürt megbízhatóság és a tartós][service-fabric-cluster-capacity].
3. A Virtuálisgép-méretet és tarifacsomagjának kiválasztása. D sorozatú virtuális gépek SSD meghajtók rendelkeznek, és erősen ajánlott állapotalapú alkalmazásokhoz. Nem használja a virtuális gép SKU részleges maggal rendelkező vagy a rendelkezésre álló szabad kapacitás 7 GB-nál kisebb. 
4. A minimális **szám** az elsődleges csomóponthoz tartozó virtuális gépek típus által vezérelt a **megbízhatóság** réteg választja. A megbízhatósági szint alapértelmezés szerint ezüst. A megbízhatóság további információkért lásd: [kiválasztása a Service Fabric-fürt megbízhatóság és a tartós][service-fabric-cluster-capacity].
5. Válassza ki a virtuális gépek számát a csomóponttípus. Méretezheti felfelé vagy lefelé a virtuális gépek számát csomóponttípus meg, de az elsődleges csomóponttípushoz, a minimális célja a megbízhatósági szint választott. Egyéb legalább 1 virtuális gép is.
6. Egyéni végpontokat konfigurálhat. Ez a mező lehetővé teszi, hogy meg kell adnia a Azure Load Balancer az alkalmazások a nyilvános interneten keresztül elérhetővé tenni kívánt portok vesszővel tagolt listája. Például ha azt tervezi, a webalkalmazások a fürt telepítéséhez, adja meg "80" Itt forgalmat engedélyezi a 80-as portot a fürtbe. A végpontok további információkért lásd: [alkalmazások folytatott kommunikáció][service-fabric-connect-and-communicate-with-services]
7. Fürt konfigurálása **diagnosztika**. Alapértelmezés szerint diagnosztika engedélyezve vannak a fürtön lévő segít a problémák elhárításához. Ha le szeretné tiltani a diagnosztika módosítása a **állapot** kapcsolót **ki**. Diagnosztika kikapcsolása van **nem** ajánlott.
8. Válassza ki a kívánt beállítása a fürt háló frissítési módot. Válassza ki **automatikus**, ha azt szeretné, hogy a rendszer automatikusan felveszi az elérhető legújabb verzióra, majd próbálja meg a fürt frissítéséhez. Beállíthatja a módot **manuális**, ha meg kívánja valamelyik támogatott verzióra.

> [!NOTE]
> Csak a service Fabric támogatott verzióit futtató fürtök támogatott. Kiválasztásával a **manuális** módban készítésének felelősségére a fürt valamelyik támogatott verzióra frissítéséhez. A további részleteket a hálót a frissítési mód lásd: a [service fabric-fürt-frissítési dokumentum.][service-fabric-cluster-upgrade]
> 
> 

#### <a name="3-security"></a>3. Biztonság
![Képernyőfelvétel az Azure-portál biztonsági beállításokkal.][SecurityConfigs]

Az utolsó lépés arra, hogy a fürt a Key Vault és információk a korábban létrehozott tanúsítvány használatával biztonságos a hitelesítő adatokat.

* Feltöltheti az elsődleges tanúsítvány mezőit és a feltöltése nyert kimenete a **fürt tanúsítvány** Key Vault használatával az `Invoke-AddCertToKeyVault` PowerShell-parancsot.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Ellenőrizze a **speciális beállítások konfigurálása** mezőben adja meg az ügyféltanúsítványok **rendszergazdai ügyfél** és **írásvédett ügyfél**. Ezekben a mezőkben adja meg a rendszergazdai ügyféltanúsítvány ujjlenyomata és az írásvédett felhasználó ügyféltanúsítvány ujjlenyomata, ha van ilyen. Ha a rendszergazdák próbál csatlakozzon a fürthöz, kapnak itt megadott hozzáférés csak akkor, ha rendelkeznek, amely megfelel az ujjlenyomat-értékeket egy ujjlenyomattal rendelkező tanúsítványt.  

#### <a name="4-summary"></a>4. Összefoglalás

A fürt létrehozásának befejezéséhez kattintson a **összegzés** tekintse meg a megadott konfiguráció, vagy töltse le az Azure Resource Manager sablon, amely a fürt telepítésére szolgál. A kötelező beállítások megadása után a **OK** gomb zöld válik, és kattintással elindíthatja a Fürtlétrehozási folyamat.

A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a „Harang” ikonra az állapotsor mellett, a képernyő jobb felső részén.) Kattintott **a kezdőpulton rögzít** megjelenik a fürt létrehozásakor **Fabric-fürt üzembe helyezése** rögzítve a **Start** tábla.

### <a name="view-your-cluster-status"></a>A fürt állapotának megtekintése
![Képernyőfelvétel a fürt részletes adatai az irányítópulton.][ClusterDashboard]

A fürt létrehozása után vizsgálhatja meg a fürt a portálon:

1. Ugrás a **Tallózás** kattintson **Service Fabric-fürtök**.
2. Keresse meg a fürthöz, és kattintson rá.
3. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

A **csomópont Monitor** szakasz a fürt irányítópult panelen, amelyek a megfelelő és nem kifogástalan állapotú virtuális gépek számát jelzi. A fürt állapotát, további információkat is talál [Service Fabric rendszerállapot-modell bemutatása][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric-fürtök egy bizonyos megkövetelése a csomópontok kell mentése mindig rendelkezésre álljon, állapot - néven "kvórum fenntartása" megőrzéséhez. Emiatt nincs általában biztonságos a fürt összes gépek leállítását, kivéve, ha először elvégezte a [teljes biztonsági mentés a állapot][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás egy virtuálisgép-méretezési csoport példányt vagy egy fürt csomópontja
A NodeType tulajdonságok értéke mindegyikének ad meg a fürt eredmény egy virtuálisgép-méretezési csoportban lévő első beállításról. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Következő lépések
Ezen a ponton hogy a biztonságos fürt felügyeleti hitelesítési tanúsítványokat használnak. Ezt követően [csatlakozzon a fürthöz](service-fabric-connect-to-secure-cluster.md) és megtudhatja, hogyan [alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).  Emellett megismerése [Service Fabric támogatási lehetőségek](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
