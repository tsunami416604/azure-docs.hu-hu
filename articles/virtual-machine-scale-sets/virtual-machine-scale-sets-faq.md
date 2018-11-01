---
title: Azure-beli virtuálisgép-méretezési csoportok – gyakori kérdések |} A Microsoft Docs
description: Virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdésekre adott válaszok.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 7b8faca82a23875b403d0b51b2e8719549d845fb
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740688"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure-beli virtuálisgép-méretezési csoportok – gyakori kérdések

Válaszok a virtual machine scale sets – gyakori kérdések az Azure-ban.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Felső a méretezési csoportokkal kapcsolatos gyakori kérdések

**K.** Hány virtuális gépet tartalmazhat egy méretezési csoport?

**V.** A méretezési csoport 0–1000, platformrendszerképen alapuló virtuális gépet, vagy 0–300, egyéni rendszerképen alapuló virtuális gépet tartalmazhat.

**K.** Támogatott az adatlemezek használata a méretezési csoportokon belül?

**V.** Igen. A méretezési csoportok meghatározhatnak egy csatlakoztatott adatlemezekből álló konfigurációt, amely a csoport összes virtuális gépére érvényes. További információ: [Azure-beli méretezési csoportok és csatlakoztatott adatlemezek](virtual-machine-scale-sets-attached-disks.md). Egyéb adattárolási lehetőségek:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Azure-adatszolgáltatás (például Azure-táblák, Azure-blobok)
* Külső adatszolgáltatás (például távoli adatbázis)

**K.** Mely Azure-régiók támogatják a méretezési csoportokat?

**V.** Mindegyik régió támogatja a méretezési csoportokat.

**K.** Hogyan lehet egyéni rendszerképekből méretezési csoportot létrehozni?

**V.** Hozzon létre egy Virtuálisgép-lemezkép rögzítése, majd forrásaként, amely a méretezési csoporthoz használni. Hogyan hozhat létre és használhat egyéni Virtuálisgép-rendszerképet oktatóanyagot, használhatja a [Azure CLI-vel](tutorial-use-custom-image-cli.md) vagy [Azure PowerShell-lel](tutorial-use-custom-image-powershell.md)

**K.** Ha a méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

**V.** A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

**K.** Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

**V.** Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például: 20, 21, 22). A virtuális gépek a tartalék és frissítési tartományok között oszlanak el.

**K.** Ha több bővítményt használok egy méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

**V.** Közvetlenül nem, bár a customScript bővítmény esetében a szkript megvárhatja egy másik bővítmény futtatásának befejezését. A végrehajtási sorrendről az [Azure-alapú virtuálisgép-méretezési csoportok végrehajtási sorrendjének beállítását](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) ismertető blogbejegyzésben talál további információt.

**K.** Használhatok virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

**V.** A regionális (nem zónaszintű) méretezési használ *elhelyezési csoportra*, amely üzemelő egy implicit rendelkezésre állási csoportot az öt tartalék tartományok és öt frissítési tartománnyal. Több mint 100 virtuális gépet tartalmazó méretezési csoportok több elhelyezési csoportra is kiterjednek. További információ az elhelyezési csoportokról: [Nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció egy rendelkezésre állási csoportba helyezi a vezérlő csomópont virtuális gépeit (ezek gyakran igényelnek egyéni konfigurálást), és a méretezési csoportba helyezi az adatcsomópontokat.

**K.** Használhatok virtuálisgép-méretezési csoportok használata az Azure rendelkezésre állási zónák?

**V.** Igen! További információkért lásd: a [méretezési zóna doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatikus méretezés

### <a name="what-are-best-practices-for-azure-autoscale"></a>Mik az Azure automatikus méretezés ajánlott eljárásai?

Ajánlott eljárások az automatikus méretezéshez, lásd: [ajánlott eljárások az automatikus skálázás virtuális gépek](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Hol találom meg a gazdagépalapú mérőszámok használatával automatikusan skálázhatja a metrikák nevei?

Gazdagépalapú mérőszámok használatával automatikusan skálázhatja a metrikák nevei, lásd: [az Azure monitorban támogatott mérőszámok](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Tudnák példákkal szemléltetni, az automatikus skálázás az Azure Service Bus témakör és az üzenetsor hossza alapján vannak?

Igen. Az automatikus skálázás az Azure Service Bus témakör és az üzenetsor hossza alapján példákért lásd [Azure Monitor automatikus skálázás gyakori metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Service Bus-üzenetsorba használja a következő JSON-ra:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Üzenetsor-tárolóba használja a következő JSON-ra:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Példaértékeket cserélje le az erőforráscsoport egységes erőforrás-azonosítók (URI-k).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Érdemes e automatikus skálázási gazdagépalapú mérőszámok vagy a diagnosztikai bővítmény használatával?

A gazdaszámítógép-szintű metrikákat, vagy az operációs rendszer Vendég mérőszámok használandó virtuális gép automatikus méretezési beállítások is létrehozhat.

A támogatott mérőszámok listája: [Azure Monitor automatikus skálázás gyakori metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Virtuálisgép-méretezési csoportokhoz tartozó teljes minta: [automatikus méretezés speciális konfigurálása Resource Manager-sablonok használatával virtuálisgép-méretezési csoportokhoz tartozó](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

A mintát használja, a gazdagépszintű CPU metrika- és a egy üzenet mérőszámot.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hogyan állíthatom be a riasztási szabályok a virtuálisgép-méretezési csoportot?

A PowerShell vagy az Azure CLI-n keresztül virtuálisgép-méretezési csoportokhoz tartozó metrikák riasztásokat is létrehozhat. További információkért lásd: [Azure Monitor PowerShell rövid minták](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) és [Azure Monitor platformfüggetlen CLI-minták a rövid útmutató](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

A virtuális gép méretezési targetresourceid azonosítója a következőhöz hasonló:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Riasztást beállítani kívánt mérőszámként lehetősége van minden olyan virtuális gép teljesítményadatait mutatja. További információkért lásd: [Resource Manager-alapú Windows virtuális gépek vendég operációs rendszer metrikáit](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) és [Linux rendszerű virtuális gépek vendég operációs rendszer metrikáit](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) a a [Azure Monitor automatikus skálázás gyakori metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)cikk.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hogyan állíthatok be az automatikus skálázás beállítása a PowerShell használatával virtuálisgép-méretezési csoportot?

Állítsa be az automatikus skálázás egy virtuális gép méretezési csoport PowerShell-lel, tekintse meg [egy virtuálisgép-méretezési csoport automatikus méretezése](tutorial-autoscale-powershell.md). Is konfigurálhatja az automatikus méretezés a [Azure CLI-vel](tutorial-autoscale-cli.md) és [Azure-sablonok](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Ha rendelkezik e leállítva (felszabadítva) egy virtuális Gépet, a virtuális gép elindítása egy automatikus skálázási művelet részeként?

Nem. Ha az automatikus skálázási szabályok további Virtuálisgép-példányok méretezési részeként van szükség, egy új Virtuálisgép-példány jön létre. Virtuálisgép-példányok, leállított (felszabadított) egy automatikus skálázási esemény részeként nem indulnak el. Azonban ezeket a leállított (felszabadított) virtuális gépek törölhetők része egy automatikus skálázási esemény, amely a példányok, méretezhető ugyanolyan módon, hogy minden Virtuálisgép-példány törölheti diagrambeli Virtuálisgép-példány azonosítóját.



## <a name="certificates"></a>Tanúsítványok

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Hogyan do I biztonságos szállításra egy tanúsítványt a virtuális géphez? Hogyan helyezhetek üzembe egy webhely, ahol a webhely az SSL nyújtják biztonságosan futtatható egy tanúsítvány konfigurálását a virtuális gép méretezési? (A közös tanúsítvány rotációja művelet lenne majdnem megegyezik a konfiguráció frissítési művelet.) Példa bemutatja, hogyan ehhez van? 

Biztonságos szállításra egy tanúsítványt a virtuális gépre, telepíthet egy ügyfél-tanúsítványt közvetlenül egy Windows-tanúsítványtároló az ügyfél-kulcstartóból való.

Használja a következő JSON-ra:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

A kód támogatja a Windows és Linux rendszereken.

További információkért lásd: [létrehozás vagy frissítés egy virtuálisgép-méretezési csoport beállítása](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Önaláírt tanúsítvány – példa

1.  Hozzon létre egy önaláírt tanúsítványt a key vaultban.

    A következő PowerShell-parancsokat használja:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Ez a parancs a bemeneti megtalálható az Azure Resource Manager-sablon.

    Egy példa bemutatja, hogyan hozzon létre egy önaláírt tanúsítványt a key vaultban: [Service Fabric-fürtök biztonsági forgatókönyveit](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  A Resource Manager-sablon módosítása

    Ez a tulajdonság hozzáadása **virtualMachineProfile**, a virtuális gép részeként méretezési erőforrás:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Meghatározható, hogy ssh-kulcs az SSH-hitelesítés használata a Resource Manager-sablon egy Linux virtuális gép méretezési?  

Igen. A REST API-val **osProfile** hasonlít a virtuális gép a szabványos REST API. 

Például **osProfile** a sablonban:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Ez a JSON-tömb szerepel [a 101-vm-ssh-kulcsfájl GitHub gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Az operációs rendszer profilja is használatban van [a grelayhost.json GitHub gyorsindítási sablon](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

További információkért lásd: [létrehozás vagy frissítés egy virtuálisgép-méretezési csoport beállítása](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Hogyan távolíthatom el az elavult tanúsítványok? 

Elavult tanúsítványok eltávolításához törölje a régi tanúsítvány tároló tanúsítványok listájából. Hagyja meg szeretné őrizni a számítógépen, a lista összes tanúsítványt. Ez nem távolítja a tanúsítványt az összes virtuális gépet. Azt is nem adható hozzá a tanúsítványt a virtuális gép méretezési csoportban létrehozott új virtuális gépeket. 

Eltávolítani a tanúsítványt a meglévő virtuális gépekről, írjon egy egyéni szkriptbővítményt manuálisan távolítsa el a tanúsítványokat a tanúsítványtárolóból.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Hogyan do I behelyezése egy meglévő nyilvános SSH-kulcs a virtuális gép méretezési készlet SSH réteg kiépítése során? Az SSH nyilvános kulcs értékeit az Azure Key Vaultban tárolni, és használja őket a saját Resource Manager-sablon szeretnék.

Ha meg van adva a virtuális gépek csak a nyilvános SSH-kulcsot, nem kell helyezni a nyilvános kulcsokat a Key Vaultban. Nyilvános kulcsok, amelyek nem titkos.
 
Egyszerű szöveges nyilvános SSH-kulcsokat a Linux rendszerű virtuális gép létrehozásakor megadhat:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
linuxConfiguration elem neve | Szükséges | Típus | Leírás
--- | --- | --- | --- |  ---
ssh | Nem | Gyűjtemény | Adja meg a Linux operációs rendszer SSH-kulcs konfigurációja
elérési út | Igen | Karakterlánc | Linux fájl elérési útját adja meg, az SSH-kulcsokat, vagy a tanúsítványt kell lennie
keyData | Igen | Karakterlánc | Adja meg a base64-kódolású SSH nyilvános kulcs

Egy vonatkozó példáért lásd: [a 101-vm-ssh-kulcsfájl GitHub gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>A futtatási amikor `Update-AzureRmVmss` után egynél több tanúsítvány felvétele a azonos key vaultban tárolt, az alábbi üzenet látható:
 
>Update-AzureRmVmss: Lista titkos kulcsot tartalmazza: /subscriptions/ < saját-subscription-id > több példánya / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, ami nem engedélyezett.
 
Ez akkor fordulhat elő, ha megpróbálja újra hozzá ugyanahhoz a tárolóhoz, a forrás meglévő tárolóhoz tartozó új tárolóra tanúsítvány használata helyett. A `Add-AzureRmVmssSecret` parancs nem működik megfelelően további titkos kulcsok hozzáadása esetén.
 
Adhat hozzá további titkos kulcsok a azonos key vault, a $vmss.properties.osProfile.secrets[0].vaultCertificates lista frissítése.
 
Tekintse meg a várt bemeneti struktúra [létrehozás vagy frissítés egy virtuálisgép-csoportot](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
A titkos kulcs található a virtuális gép méretezési készlet objektum, amely a key vaultban. Adja hozzá a a listában, a tárolóhoz rendelt tanúsítvány hivatkozást (URL-CÍMÉT és a titkoskód-tárolót neve).

> [!NOTE] 
> Jelenleg nem távolítható el tanúsítványok virtuális gépekhez a virtuális gép méretezési készlet API-val.
>

Új virtuális gépek nem fog a régi tanúsítványt. Azonban a virtuális gépeket, amely rendelkezik a tanúsítványt, és amely már üzembe helyezte a régi tanúsítvány lesz.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Képes tanúsítványokat is leküldése a anélkül, hogy a jelszót, ha a tanúsítvány a titkoskód-tárolót a virtuálisgép-méretezési?

Nem kell kódolnia jelszavak parancsfájlokban. Dinamikusan kérheti le az engedélyeket, az üzembe helyezési parancsfájl futtatásához használt jelszavakat. Ha egy parancsfájl, amely egy tanúsítványt a titkoskód-tárolót kulcs helyezi át a tárolót, a titkoskód-tárolót `get certificate` parancsot is megjeleníti a jelszót a .pfx-fájl.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hogyan állítható be a virtualMachineProfile.osProfile virtuálisgép-méretezési csoportot a titkos kulcsok tulajdonságát munkahelyi? Miért kell meg sourceVault értékét, ha a certificateUrl tulajdonság használatával adja meg a tanúsítvány abszolút URI Azonosítónak kell? 

Windows Rendszerfelügyeleti (webszolgáltatások WinRM) tanúsítvány hivatkozást az operációs rendszer profilja titkok tulajdonságában elérhetőnek kell lennie. 

Jelzi, a forrás-tároló célja, hogy a hozzáférés-vezérlési lista (ACL) házirendeket, amelyek szerepelnek a felhasználó az Azure Cloud Service-modell kényszerítése. Ha a forrás-tároló nincs megadva, üzembe helyezése, vagy a titkos kulcsok a key vault hozzáférési engedélyekkel nem rendelkező felhasználók tudná keresztül a számítási erőforrás szolgáltató (CRP). Hozzáférés-vezérlési listák még nem létező erőforrások léteznek.

Egy nem megfelelő tároló Forrásazonosítója azonban egy érvényes key vault URL-címet ad meg, ha hibajelentést küld, amikor meg lekérdezni a műveletet.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Ha a titkos kulcsok hozzáadni egy meglévő virtuálisgép-méretezési csoport beállítása, a titkos kódok szúrhatja be a meglévő virtuális gépeket, vagy újakat csak? 

Az összes virtuális gépet, előre meglévők is hozzáadja a tanúsítványokat. Ha a virtuális gép méretezési upgradePolicy tulajdonság értéke **manuális**, a virtuális géphez hozzáadta a tanúsítványt, amikor a virtuális gép manuális frissítést végez.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Hol helyezhetem el a tanúsítványok Linux rendszerű virtuális gépekhez?

Tanúsítványok telepítése Linux rendszerű virtuális gépek kezelésével kapcsolatos információkért lásd: [tanúsítványok telepítése a virtuális gépek az ügyfél által felügyelt key vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hogyan adhatok hozzá egy új tárolót tanúsítvány egy új tanúsítvány objektumot?

A tároló tanúsítvány hozzáadása egy meglévő titkos kulccsal, tekintse meg a következő PowerShell-példa. Csak egy titkos objektum használható.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Mi történik a tanúsítványok, ha a virtuális gép rendszerképének alaphelyzetbe állítása?

Ha, újból lemezképet létrehozni egy virtuális Gépet, a tanúsítványok törlődnek. Törli a teljes operációs rendszer lemez alaphelyzetbe. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Mi történik, ha törli egy tanúsítványt a key vaultban tárolt?

Ha a titkos kulcsot a kulcstartóban található törlődik, és futtassa azt `stop deallocate` a virtuális gépek számára, és ezután indítsa újra őket, hibát tapasztal. A hiba akkor fordul elő, mert a KSZT a titkos kódok lekérése a key vault szükséges, de nem érhető el. Ebben a forgatókönyvben törölheti a tanúsítványokat, a virtuálisgép-méretezési csoport modelljéből. 

A CRP-összetevő nem marad az ügyfél titkos kulcsok. Ha `stop deallocate` a virtuálisgép-méretezési csoportban lévő összes virtuális gép, törlődik. Ebben a forgatókönyvben a titkos kulcsok a key vaultban tárolt lekérdez.

Ez a probléma nem tapasztal, amikor a horizontális felskálázás, mert a titkos kulcsot az Azure Service Fabric (a modellben egyetlen fabric-bérlő) gyorsítótárazott másolatának.
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Miért van, adja meg a pontos helyét a tanúsítvány URL-címe (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), ahol azt az [Service Fabric-fürtök biztonsági forgatókönyveit](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Az Azure Key Vault-dokumentáció megállapítja, hogy az első titkos REST API a titkos kulcs legújabb verzióját kell visszaadnia, ha a verzió nincs megadva.
 
Módszer | URL-cím
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Cserélje le a(z)*titkos kulcs-name*} a nevét, és cserélje le a {*titkos kulcs-verzió*} verziójával, a titkos kulcsot szeretné beolvasni. Előfordulhat, hogy ki kell zárni a titkos kulcs verzióját. Ebben az esetben lekéri a jelenlegi verziót.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Miért kell megadnia a verziójának Key Vault használata esetén?

A Key Vault követelmény a verziójának megadása az a célja, hogy győződjön meg arról, hogy egyértelmű, hogy a felhasználó milyen tanúsítvány telepítve van a virtuális gépeiken.

Ha létrehoz egy virtuális Gépet, és frissítse a titkos kulcsot a kulcstartóban, az új tanúsítvány nem tölti le a virtuális gépekhez. De a virtuális gépek arra mutató hivatkozás jelenik meg, és új virtuális gépeket az új titkos kód beolvasása. Ennek elkerülése érdekében való hivatkozáshoz egy titkos kód verziója is szükséges.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A csapatom együttműködik számos tanúsítványok nyilvános kulcsát .cer, elosztott velünk a kapcsolatot. Milyen értéke az ajánlott módszer, ezek a tanúsítványok telepítéséhez a virtuálisgép-méretezési csoportot?

.Cer üzembe egy virtuálisgép-méretezési csoport nyilvános kulcsok beállítása, és létrehozhat egy .pfx-fájlt, amely csak a .cer fájlokat tartalmazza. Ehhez használja `X509ContentType = Pfx`. Például a .cer fájl betöltése x509Certificate2 objektumként C# vagy a PowerShell, és ezután a metódus meghívására. 

További információkért lásd: [X509Certificate.Export metódus (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Miért nem jelenik meg egy beállítást, a felhasználók számára tanúsítványok Base64 kódolású karakterláncként adja át. A legtöbb más erőforrás-szolgáltatók kell ezt a beállítást.

A tanúsítvány Base64 kódolású karakterláncként ad emulációjához, kibonthatja a legújabb verzióval ellátott URL-címet a Resource Manager-sablonnal. A Resource Manager-sablon a következő JSON-tulajdonságot tartalmazza:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Rendelkezik tanúsítványok kulcstartók JSON-objektumában wrap funkciót?

A virtuálisgép-méretezési csoportok és a virtuális gépek tanúsítványokat kell csomagolni, a JSON-objektumok. 

Az application/x-pkcs12 tartalomtípus is támogatja. Application/x-pkcs12 használatával kapcsolatos utasításokért lásd: [PFX-tanúsítványok az Azure Key Vaultban](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Jelenleg nem támogatott .cer fájlokat. Használja a .cer fájl formájában, exportálja őket .pfx tárolókba.



## <a name="compliance-and-security"></a>Megfelelőségi és biztonsági

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Azok a virtuálisgép-méretezési csoportok PCI-követelményeknek?

A virtuálisgép-méretezési csoportok egy vékony API-réteget alkotnak a KSZT tetején. Mindkét összetevő a számítási platform részét képezi az Azure-szolgáltatások rendszerében.

A megfelelőség szempontjából nézve a virtuálisgép-méretezési csoportok az Azure számítási platformjának alapvető részét képezik. Ezek a csoportok ugyanazt csapatot, eszközöket, folyamatokat, üzembe helyezési módszereket, biztonsági vezérlőket, igény szerinti (JIT) fordításokat, felügyeletet, riasztásokat stb. használják, mint maga a KSZT. A virtuálisgép-méretezési csoportok megfelelnek a PCI-szabványnak, mert a KSZT része a jelenlegi PCI Data Security Standard (DSS) igazolásnak.

További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Does [felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/msi-overview) munkahelyi virtuálisgép-méretezési csoportok?

Igen. Egyes MSI példasablonkészlet az Azure-gyorssablonok látható. Linux: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Bővítmények

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hogyan törölhetek egy virtuálisgép-méretezési csoport bővítményének?

Egy virtuálisgép-méretezési csoport bővítményének törléséhez használja a következő PowerShell-példa:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Az bővítménynév értéket annak `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Egy virtuálisgép-méretezési csoport sablon példa, amely integrálható a Log Analytics beállítása?

Egy virtuálisgép-méretezési csoport sablon példa, amely integrálható a Log Analytics beállítása, tekintse meg a második példáját [egy Azure Service Fabric-fürt üzembe helyezése és figyelése a Log Analytics használatával engedélyezése](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Bővítmények úgy tűnik, hogy a virtual machine scale sets párhuzamosan futnak. Ennek hatására a saját egyéni szkriptek futtatására szolgáló bővítmény sikertelen lesz. Mire használhatom a probléma megoldásához?

A virtual machine scale sets sorrendről kapcsolatos további információkért lásd: [az Azure-beli virtuálisgép-méretezési csoportok végrehajtási](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hogyan do I alaphelyzetbe állíthatja a jelszót a virtuális gépek saját virtuálisgép-méretezési csoportban lévő?

Módosíthatja a jelszót a virtuális gépek méretezési két fő módja van.

- Közvetlenül módosítsa a virtuálisgép-méretezési csoport modelljéből. A Compute API 2017-12-01 vagy újabb érhető el.

    Frissítse közvetlenül a méretezési csoport modelljéből (például az Azure Resource Explorer, a PowerShell vagy a parancssori felület használatával) a rendszergazdai hitelesítő adatait. Amint a méretezési frissített, minden új virtuális gépek rendelkeznek az új hitelesítő adatokkal. Meglévő virtuális gépek csak az új hitelesítő adatokkal rendelkezik, ha azok rendszerképét alaphelyzetbe állítják. 

- Alaphelyzetbe a jelszót a hozzáférést a Virtuálisgép-bővítmények használatával.

    Használja a következő PowerShell-példát:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hogyan adhatok hozzá egy bővítmény az összes virtuális gépre a saját virtuálisgép-méretezési csoportot?

Ha a frissítési szabályzat lesz beállítva **automatikus**, minden virtuális gép újbóli üzembe helyezés a sablont az új bővítménytulajdonságok a frissítések.

Ha a frissítési szabályzat lesz beállítva **manuális**, először frissítse a bővítményt, és frissítse manuálisan a virtuális gépek szereplő összes példányt.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>A bővítmények egy meglévő virtuálisgép-méretezési társított frissülnek, ha a virtuális gépeit érinti a meglévő? (Azaz lesz a virtuális gépek *nem* felel meg a virtuálisgép-méretezési csoport modelljéből?) Vagy azok mellőz a rendszer? Ha egy meglévő gépre a szolgáltatás-kezelte, vagy rendszerképét alaphelyzetbe állítják, azok a parancsprogramok, végrehajtott virtuálisgép-méretezési vannak beállítva, vagy azokat a parancsprogramokat, ha a virtuális gép létrehozásakor konfigurált használni?

Ha a bővítmény a virtuálisgép-méretezési csoport definíciójában modell frissül, és a upgradePolicy tulajdonság értéke **automatikus**, a virtuális gépeket frissíti. Ha a upgradePolicy tulajdonság értéke **manuális**, bővítmények megjelölt a modell nem egyeznek. 

Ha egy meglévő virtuális gép szolgáltatás kezelte, újraindítás jelenik meg, és a bővítményeket nem Újrafuttatja. Ha rendszerképét alaphelyzetbe állítják, például az operációs rendszer meghajtójának cserélje le a forrás lemezkép van. Minden olyan bővítmények, mint például a legújabb modellből specializáció futnak.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hogyan csatlakozzon a Active Directory-tartomány beállítása egy virtuálisgép-méretezési csoport?

Egy virtuálisgép-méretezési csoportba az Active Directory (AD) tartományhoz csatlakozni, meghatározhatja egy bővítmény. 

Egy bővítmény meghatározásához használja a JsonADDomainExtension tulajdonság:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Saját virtuálisgép-méretezési csoport bővítményének próbálja meg telepíteni, hogy újra kell indítani. Például: "commandToExecute": "powershell.exe - ExecutionPolicy Unrestricted Install-WindowsFeature-Name FS-Resource-Manager – IncludeManagementTools"

Ha a virtuálisgép-méretezési csoport bővítményének próbálja meg telepíteni, hogy újra kell indítani, használhatja az Azure Automation Desired State Configuration (Automation DSC) bővítmény. Ha az operációs rendszer Windows Server 2012 R2, az Azure lekéri az újraindítások, a Windows Management Framework (WMF) 5.0 telepítés, és majd folytatja az a konfiguráció. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hogyan kapcsolhatom a kártevőirtó saját virtuálisgép-méretezési csoportban lévő?

A virtuálisgép-méretezési kártevőirtó bekapcsolása, használja a következő PowerShell-példa:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Privát storage-fiókban lévő üzemeltetett egyéni parancsfájl végrehajtására van szükségem. A szkript sikeresen lefutott, amikor a tároló nem nyilvános, de jelenik meg a közös hozzáférésű Jogosultságkód (SAS) használni, akkor sem jár sikerrel. Ez az üzenet jelenik meg: "Hiányzik a kötelező paraméterekhez tartozó érvényes közös hozzáférésű Jogosultságkód". Hivatkozás + SAS jól működik a helyi böngészőből.

Privát storage-fiókban lévő üzemeltetett egyéni parancsfájl végrehajtása védett beállítások megadása a tárfiók kulcsát, a név. További információkért lásd: [Custom Script bővítmény a Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Hálózat
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Ennyi az egész hálózati biztonsági csoport (NSG) hozzárendelése egy méretezési csoportot, lehetséges, hogy a készletben lévő összes virtuális gép hálózati vonatkozik?

Igen. A hálózati biztonsági csoportok közvetlenül egy méretezési csoport hálózati profiljának networkInterfaceConfigurations szakaszában hivatkozva kell alkalmazni. Példa:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hogyan hajthatok végre egy virtuális IP-címcsere a virtuális gép méretezési csoportokhoz az ugyanahhoz az előfizetéshez, és ugyanabban a régióban?

Ha két virtuálisgép-méretezési csoportok az Azure Load Balancer az előtér-kiszolgálókon, és azok, azonos előfizetésben és régióban, sikerült szabadítsa fel az egyes nyilvános IP-címét, és hozzárendelheti a másik. Lásd: [virtuális IP-Címcsere: kék-zöld üzembe helyezés az Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) például. Ez hasonló a késést, ha az erőforrások vannak a hálózaton, felszabadítva vagy lefoglalt szintű. A gyorsabb megoldás, ha az Azure Application Gateway használatára két háttérkészletek, és a egy útválasztási szabályt. Azt is megteheti, hogy sikerült az alkalmazások üzemeltetéséhez [Azure App Service-ben](https://azure.microsoft.com/services/app-service/) amely támogatást biztosít a gyors váltás átmeneti és éles pontok között.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hogyan határozhatom meg magánhálózati IP-címek statikus magánhálózati IP-cím lefoglalását használandó számos?

IP-címek ki van jelölve, az Ön által megadott alhálózat. 

A virtuális gép méretezési készlet IP-cím-kiosztási módszerét mindig "dinamikus", de ez nem jelenti azt, hogy ezen IP-címek módosíthatja. Ebben az esetben "dinamikus" csak azt jelenti, hogy nincs megadva az IP-címet a PUT-kérelmekben. Adja meg a statikus beállítása az alhálózat használatával. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hogyan helyezhetek üzembe egy meglévő Azure virtuális hálózat beállítása egy virtuálisgép-méretezési csoport? 

Egy meglévő Azure virtuális hálózat beállítása egy virtuálisgép-méretezési csoport üzembe helyezéséhez lásd: [üzembe helyezése virtuálisgép-méretezési csoportot a meglévő virtuális hálózat beállítása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Hogyan adhatok hozzá az első virtuális gép IP-címét egy virtuális gép méretezési sablon kimenete?

Adja hozzá az első virtuális gép IP-címét egy virtuális gép méretezési sablon kimenete, lásd: [Azure Resource Manager: Get virtuálisgép-méretezési csoportok magánhálózati IP-címek](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>A méretezési csoportok használata a gyorsított hálózatkezelés?

Igen. A gyorsított hálózatkezelés használata, állítsa az enableacceleratednetworking tulajdonságot igaz értékre a méretezési csoportban lévő csoport networkInterfaceConfigurations beállításaiban. Példa:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hogyan konfigurálható egy méretezési csoportot által használt DNS-kiszolgálók?

Egyéni DNS-konfigurációval rendelkező virtuális gép méretezési csoportot létrehozni, adja hozzá a dnsSettings JSON-csomagot a méretezési csoport networkInterfaceConfigurations szakaszához. Példa:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hogyan konfigurálhatok nyilvános IP-cím hozzárendelése minden egyes Virtuálisgép-méretezési?

Hozzon létre egy virtuális gép méretezési csoportot, amely a nyilvános IP-címet rendel minden egyes virtuális Géphez, ellenőrizze, hogy a Microsoft.Compute/virtualMachineScaleSets erőforrás API-verziója 2017-03-30, és adjon hozzá egy _publicipaddressconfiguration_ JSON csomag a méretezési csoport ipConfigurations szakaszához beállítása. Példa:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Konfigurálhatja egy méretezési csoport használata több Application Gateway átjárókkal?

Igen. Erőforrás azonosítója a több Application Gateway háttér-címkészletet, adhat hozzá a _applicationGatewayBackendAddressPools_ listájában a _IP-konfigurációk_ szakasz a méretezési csoport hálózati beállítása profil.

## <a name="scale"></a>Méretezés

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Milyen esetben szeretne létrehozni egy virtuális gép méretezési kevesebb mint két virtuális gépet?

Hozhat létre egy virtuálisgép-méretezési csoportot kevesebb mint két virtuális gépet az egyik oka lehet egy virtuálisgép-méretezési csoport rugalmas tulajdonságainak használata. Például telepíthet egy virtuálisgép-méretezési csoport virtuális gépek virtuális gépek anélkül az infrastruktúra meghatározásához állítsa be. Amikor készen áll a virtuális gépek üzembe helyezése, majd, növelje az helyezendő példányok beállítása a virtuálisgép-méretezési csoport "kapacitását".

Létrehozhat egy virtuális gép méretezési kevesebb mint két virtuális gépet egy másik ok az, hogy az érintett kevesebb a rendelkezésre állás, mint a használatával egy rendelkezésre állási csoportot használ különálló virtuális gépekkel. Virtuálisgép-méretezési csoportok lehetővé teszik, a magánháztartás számítási egységekkel dolgozhat. Ez az egységesség fontos különbséget jelent a virtuális gép méretezési csoportok és a rendelkezésre állási csoportokat. Sok állapot nélküli munkaterhelés nem követik nyomon az egyes egységeket. Ha a munkaterhelés csökken, vertikális leskálázás egy számítási egységbe, és majd vertikális felskálázás több megnő.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hogyan változtatható meg a virtuálisgép-méretezési csoportban lévő virtuális gépek száma?

Ha módosítani szeretné az Azure Portal virtuális gép méretezési csoportban lévő virtuális gépek számát, a virtuálisgép-méretezési csoport tulajdonságok szakaszában állítsa be, kattintson a a "Méretezés" panel és a csúszkával. Módosítsa a példányszám egyéb módjai, lásd: [módosítása egy virtuálisgép-méretezési csoportot példányainak számát](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hogyan határozza meg az egyéni riasztások bizonyos küszöbértékek elérésekor?

Bizonyos fokú szabadsága van a hogyan kezeli a megadott küszöbértékek kapcsolatos riasztások. Például megadhatja a testre szabott webhookok. A következő webhook példája egy Resource Manager-sablon:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

Ebben a példában egy riasztás kerül Pagerduty.com a küszöbérték elérésekor.



## <a name="patching-and-operations"></a>Javítás és műveletek

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Hogyan hozhatok létre egy méretezési csoportot egy meglévő erőforráscsoportban?

Méretezési csoportok létrehozása a meglévő erőforrás csoport még nem lehetséges az Azure Portalról, de megadhat egy meglévő erőforráscsoportot, ha a méretezési csoport üzembe helyezése az Azure Resource Manager-sablon beállítása. Egy meglévő erőforráscsoportot is adja meg, amikor hoz létre egy méretezési csoportot az Azure PowerShell vagy parancssori felület használatával.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Nem áthelyezni egy méretezési csoportot egy másik erőforráscsoportba?

Igen, áthelyezheti a méretezési erőforrások új előfizetéshez vagy erőforráscsoporthoz.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hogyan, frissíthetők a saját virtuálisgép-méretezési csoportba az új lemezképet? Hogyan kezelhetem a javítás?

Frissítse a virtuális gép méretezési egy új rendszerképet, és kezelje a javításokat, lásd: [frissíteni egy virtuálisgép-méretezési csoportot](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>A rendszerkép alaphelyzetbe állítására használatával virtuális gépek visszaállítása a kép módosítása nélkül? (Azaz szeretnék egy virtuális gép visszaállítása a gyári beállításokra helyett az új lemezképet.)

Igen, használhatja a rendszerkép alaphelyzetbe állítására alaphelyzetbe állítani a virtuális gép a lemezkép módosítása nélkül. Azonban, ha a virtuális gép méretezési hivatkozik platformlemezképen `version = latest`, a virtuális gép is lehet frissíteni egy újabb operációsrendszer-lemezkép hívásakor `reimage`.

További információkért lásd: [virtuálisgép-méretezési csoportban lévő összes virtuális gép kezelése](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Az is integrálhatók a méretezési csoportok az Azure Log Analytics?

Igen, a méretezési csoport a Log Analytics-bővítmény telepítésével állíthatja be virtuális gépeket. A következő Azure CLI-példa:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
A szükséges munkaterület azonosítója és workspaceKey a Log Analytics-munkaterületet az Azure Portalon találhatja. Az Áttekintés lapon kattintson a beállítások csempe. Kattintson a csatlakoztatott források fülre az oldal tetején.

Megjegyzés: Ha a méretezési csoport _upgradePolicy_ értéke kézi, meg kell alkalmaznia a bővítmény a csoport összes virtuális gépére a frissítés meghívásával rajtuk. CLI-ben ez lenne _az vmss update-instances_.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hogyan engedélyezze a rendszerindítási diagnosztikát?

A rendszerindítási diagnosztika bekapcsolása, először hozzon létre egy storage-fiókot. Ezután helyezze a JSON-kódblokkot a virtuálisgép-méretezési csoportban lévő **virtualMachineProfile**, és frissíti a virtuálisgép-méretezési csoportot:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Új virtuális gép létrehozásakor, a virtuális gép az InstanceView tulajdonság a képernyőképe, és így tovább részleteit jeleníti meg. Például:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hogyan szerezhetem be az egyes virtuális Gépekhez a tulajdonság adatait anélkül, hogy több hívást? Például hogyan kellene jelenik meg a tartalék tartomány minden egyes 100 virtuális saját virtuálisgép-méretezési csoportban lévő?

Az egyes virtuális Gépekhez a tulajdonság adatait anélkül, hogy több hívást lekéréséhez hívja `ListVMInstanceViews` egy REST API-ra végrehajtásával `GET` meg a következő erőforrás-URI:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>E argumentumok adhatók át másik kiterjesztést másik virtuális gépet egy virtuálisgép-méretezési csoportot?

Virtuálisgép-méretezési csoportban lévő különböző virtuális gépekhez nem, nem adhatók át másik kiterjesztést argumentumokat. Azonban bővítmények működhet-e az azokat futtató, például a számítógép neve a virtuális gép egyedi tulajdonságai alapján. Bővítmények is lekérdezheti példány-metaadat a http://169.254.169.254 a virtuális gép további információt szeretne kapni.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Miért vannak-e hiányosságok a saját virtuális gép méretezési csoport virtuális gép neveinek és a virtuális gép azonosítók között? Például: 0, 1, 3...

Nincsenek szünetek a virtuális gép méretezési csoport virtuális gép neveinek és a virtuális gép azonosítók között, mert a virtuálisgép-méretezési **overprovision** tulajdonság értéke az alapértelmezett érték **igaz**. Ha túlzott értéke **igaz**, a kért jönnek létre több virtuális gépet. A további virtuális Gépekért törlődnek. Ebben az esetben, így nagyobb üzembe helyezés megbízhatóságát, de rovására összefüggő elnevezése és összefüggő hálózati címfordítás (NAT) szabályok. 

És ez a tulajdonság megadható **hamis**. Kisméretű virtuálisgép-méretezési csoportokhoz üzembe helyezés megbízhatóságát jelentősen ez nem befolyásolja.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Mi a különbség egy virtuális gép méretezési csoportban lévő virtuális gépek törlése és a virtuális gép felszabadítása? Mikor válasszam közülük?

A virtuálisgép-méretezési csoportban lévő virtuális gépek törlése és a virtuális gép felszabadítása közötti fő különbség az, hogy `deallocate` nem törli a virtuális merevlemezek (VHD). Futó társított storage-díjak `stop deallocate`. Használhatja az egyiket a következő okok valamelyike:

- Meg szeretné szüntetni a számítási költségek, de a lemez állapotát, a virtuális gépek megtartja.
- El szeretné indítani a virtuális gépek gyorsabban sikerült horizontális felskálázás egy virtuálisgép-méretezési csoportot.
  - A forgatókönyvhöz kapcsolódó, előfordulhat, hogy létrehozta a saját automatikus skálázási motor és a egy gyorsabb végpontok közötti skálán szeretné.
- Rendelkezik egy virtuális gép méretezési csoportot, amely a egyenetlenül oszlanak el a tartalék tartomány vagy a frissítési tartományok között. Ez lehet, mert a szelektíven törölt virtuális gépek vagy virtuális gépek után túlzott törölve lett. Futó `stop deallocate` követ `start` a virtuális gép méretezési csoportot egyenlően osztja el a virtuális gépek tartalék tartomány és frissítési tartományok között.

