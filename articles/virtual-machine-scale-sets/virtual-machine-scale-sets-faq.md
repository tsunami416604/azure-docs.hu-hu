---
title: Az Azure virtuálisgép-skálázási készletekben – gyakori kérdések |} Microsoft Docs
description: Virtuálisgép-méretezési csoportok gyakran feltett kérdésekre adott válaszok.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.custom: na
ms.openlocfilehash: 2b0f463c009d13440f6d3eb2bbbe2315ba7b13f2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Az Azure virtuálisgép-skálázási készletekben – gyakori kérdések

Válaszok virtuálisgép-méretezési csoportok kapcsolatos gyakori kérdések az Azure-ban.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Gyakori kérdések a méretezési készlet felső
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

**V.** Hozzon létre egy felügyelt lemezt az egyéni rendszerkép VHD-fájlja alapján, és hivatkozzon arra a méretezési csoport sablonjában. [Például:](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)

**K.** Ha a méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

**V.** A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

**K.** Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

**V.** Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például: 20, 21, 22). A virtuális gépek a tartalék és frissítési tartományok között oszlanak el.

**K.** Ha több bővítményt használok egy méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

**V.** Közvetlenül nem, bár a customScript bővítmény esetében a szkript megvárhatja egy másik bővítmény futtatásának befejezését. A végrehajtási sorrendről az [Azure-alapú virtuálisgép-méretezési csoportok végrehajtási sorrendjének beállítását](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) ismertető blogbejegyzésben talál további információt.

**K.** Használhatok virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

**V.** A regionális (nem zonal) méretezési használ *elhelyezési csoportok*, amelyek beállítható úgy, hogy működjön, és állítsa be az öt tartalék tartományok implicit rendelkezésre állási és öt tartományok frissítése. 100-nál több virtuális gépek méretezési csoportok span több elhelyezési csoportot. További információ az elhelyezési csoportokról: [Nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció egy rendelkezésre állási csoportba helyezi a vezérlő csomópont virtuális gépeit (ezek gyakran igényelnek egyéni konfigurálást), és a méretezési csoportba helyezi az adatcsomópontokat.

**K.** Tegye méretezni a zónákat az Azure rendelkezésre állási készletek használata?

**V.** Igen! További információkért lásd: a [méretezési zóna doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatikus méretezés

### <a name="what-are-best-practices-for-azure-autoscale"></a>Mik az Azure automatikus skálázás ajánlott eljárásai?

Ajánlott eljárások az automatikus skálázás, lásd: [ajánlott eljárások a virtuális gépek automatikus skálázás](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Hol található a következő automatikus skálázás állomásalapú metrikák használó metrika neve?

Automatikus skálázás állomásalapú metrikák használó mérték nevét, lásd: [támogatott Azure-figyelő metrikák](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Vannak-e az Azure Service Bus témakör és a várólista hossza alapján automatikus skálázás bármely példái?

Igen. Az Azure Service Bus témakör és a várólista hossza alapján automatikus skálázás példákért lásd [Azure figyelő automatikus skálázás közös metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

A Service Bus-üzenetsorba használja a következő JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Tároló várólista használja a következő JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Cserélje a erőforrás egységes erőforrás-azonosítók (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Kell I Automatikus méretezéssel állomásalapú metrikák vagy a diagnosztika bővítmény használatával?

Az automatikus skálázási beállítás hozhat létre a virtuális gép a gazdagép szintekhez tartozó metrikákat vagy a vendég operációs rendszer-alapú metrikákat.

A támogatott mérőszámok listájáért lásd: [Azure figyelő automatikus skálázás közös metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

A teljes minta a virtuálisgép-méretezési csoportok, lásd: [speciális automatikus skálázás konfigurációs Resource Manager-sablonok használatával virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

A minta a gazdaszintű CPU metrika és üzenet mérőszámot használja.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hogyan állíthatom riasztási szabályok a virtuálisgép-méretezési csoportot?

A PowerShell vagy az Azure parancssori felület használatával virtuálisgép-méretezési csoportok metrikáját riasztásokat hozhat létre. További információkért lásd: [figyelő PowerShell Azure gyors üzembe helyezés minták](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) és [Azure figyelő platformfüggetlen parancssori felület gyors üzembe helyezés minták](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

A virtuálisgép-méretezési csoport targetresourceid azonosítója így néz ki: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

A metrika a riasztások, dönthet úgy a virtuális gép teljesítményszámláló. További információkért lásd: [Resource Manager-alapú Windows virtuális gépek vendég operációs rendszer metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) és [Linux virtuális gépek vendég operációs rendszer metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) a a [Azure figyelő automatikus skálázás közös metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) cikk.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hogyan állíthatom be automatikus skálázás a PowerShell segítségével állítsa be a virtuálisgép-méretezési?

A virtuálisgép-méretezési PowerShell segítségével állítsa be automatikus skálázás beállításához tekintse meg a következő blogbejegyzésben található [automatikus skálázás felvétele az Azure virtuálisgép-méretezési csoport](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Tanúsítványok

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Hogyan tegye szeretnék biztonságosan küldje el a virtuális gép tanúsítványt? Hogyan kiépíteni a futtatásához egy webhelyet, ahol a webhely SSL nyújtják biztonságosan tanúsítvány konfigurációról beállítása virtuálisgép-méretezési? (A közös tanúsítvány forgatási művelet lenne majdnem megegyezik a konfiguráció-frissítési művelet.) Példa bemutatja, hogyan ehhez van? 

Biztonságos módon küldje el a virtuális gép egy tanúsítványt, a felhasználói tanúsítvány is telepítheti közvetlenül tanúsítványtárolóba a Windows a vásárlói kulcstároló.

Használja a következő JSON:

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

A kód a Windows és Linux támogatja.

További információkért lásd: [létrehozás vagy frissítés egy virtuálisgép-méretezési készlet](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Önaláírt tanúsítvány – példa

1.  Hozzon létre egy önaláírt tanúsítványt kulcstároló.

    A következő PowerShell-parancsokat használja:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Ez a parancs lehetővé teszi a bemeneti az Azure Resource Manager sablonhoz.

    Például egy önaláírt tanúsítvány létrehozása a kulcstároló, lásd: [Service Fabric-fürt biztonsági forgatókönyvek](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  A Resource Manager-sablon módosítása

    Adja hozzá ezt a tulajdonságot **virtualMachineProfile**, részeként a virtuálisgép-méretezési készlet erőforrás:

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
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Megadhatja egy SSH-kulcspárral állítson be úgy a Resource Manager-sablon a Linux virtuális gép skálával SSH hitelesítés használatához?  

Igen. A REST API-t a **osProfile** hasonlít a szabványos VM REST API-t. 

Tartalmaznak **osProfile** a sablonban:

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
 
Ez a JSON-tömb használatos [a 101-vm-ssh-kulcsfájl GitHub következő gyorsindítási sablonon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Az operációsrendszer-profil is van használatban [a grelayhost.json GitHub következő gyorsindítási sablonon](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

További információkért lásd: [létrehozás vagy frissítés egy virtuálisgép-méretezési készlet](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Hogyan távolíthatom elavult tanúsítványok? 

Távolítja el az elavult, távolítsa el a régi tanúsítvány a tárolóban tanúsítványok listájából. Hagyja meg szeretné őrizni a listában a számítógép összes tanúsítványt. Ez a virtuális gépek nem távolítja el a tanúsítványt. Azt is nem adja hozzá a tanúsítványt a virtuálisgép-méretezési csoportban lévő létrehozott új virtuális gépeket. 

A tanúsítvány eltávolítása a meglévő virtuális gépek, írni egy egyéni parancsprogramok futtatására szolgáló bővítmény távolíthatja el kézzel a tanúsítványokat a tanúsítványtárolóból.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Hogyan do I behelyezése egy meglévő nyilvános SSH-kulcsot a virtuális gép méretezési készlet SSH réteg kiépítése során? Az SSH nyilvános kulcs értékei tárolása az Azure Key Vault, és ezeket a Resource Manager-sablon majd használni kívánt.

Ha meg van adva a virtuális gépek csak a nyilvános SSH-kulcsot, nem kell a nyilvános kulcsok be kulcstároló. Nyilvános kulcsai nem titkos.
 
Megadhat egyszerű szöveges nyilvános SSH-kulcsok Linux virtuális gép létrehozásakor:

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
ssh | Nem | Gyűjtemény | Adja meg a Linux operációs rendszert futtató SSH-kulcs konfigurációja
elérési út | Igen | Karakterlánc | Ha az SSH-kulcsok vagy tanúsítványt kell elhelyezkedniük Linux elérési
keyData | Igen | Karakterlánc | Megadja a base64-kódolású nyilvános SSH-kulcs

Egy vonatkozó példáért lásd: [a 101-vm-ssh-kulcsfájl GitHub következő gyorsindítási sablonon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Futtatásakor `Update-AzureRmVmss` egynél több tanúsítvány hozzáadása az azonos kulcsú tárolóból, után látható a következő üzenetet:
 
>Frissítés-AzureRmVmss: Lista titkos kulcsot tartalmaz többszöri /subscriptions/ < saját előfizetés-azonosító > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, ami nem engedélyezett.
 
Ez akkor fordulhat elő, ha kísérli meg újra hozzá az új tároló tanúsítvány helyett a meglévő forrás-tároló azonos tárolóban. A `Add-AzureRmVmssSecret` parancs nem működik megfelelően, ha további titkok hozzáadni.
 
Az azonos kulcstároló további titkok hozzáadásához a $vmss.properties.osProfile.secrets[0].vaultCertificates lista frissítése.
 
Tekintse meg a várt bemeneti szerkezetből [létrehozás vagy frissítés egy virtuálisgép-csoport](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
A titkos kulcs található a virtuális gép méretezési készlet objektum, amely a kulcstartót. Adja hozzá a tárolóhoz rendelt lista a tanúsítvány hivatkozás (az URL-cím és a titkos nevét).

> [!NOTE] 
> Jelenleg nem távolítható el tanúsítványok virtuális gépek a virtuális gép méretezési készlet API használatával.
>

Új virtuális gép nem fog a régi tanúsítvány. A tanúsítvány van, és a már telepített virtuális gépek azonban a régi tanúsítvány rendelkezik.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Képes tanúsítványokat is leküldéses a virtuális gép méretezése nélkül a jelszó megadása, ha a tanúsítvány titkos tárolójában?

Nem kell kódolnia jelszavak parancsfájlokban. A telepítési parancsfájl futtatásához használt engedélyeivel jelszavak dinamikusan kérheti le. Ha egy parancsfájlt, mely az egy tanúsítványt a titkos a tároló kulcsa használó, a titkos tároló `get certificate` parancs is kiírja a jelszót a .pfx fájl.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hogyan nem a titkos kulcsok a virtuálisgép-méretezési virtualMachineProfile.osProfile tulajdonsága munkahelyi? Miért kell meg sourceVault értékét, ha adja meg a tanúsítvány abszolút URI-JÁNAK certificateUrl tulajdonságának használatával kell? 

A Rendszerfelügyeleti (webszolgáltatások WinRM) tanúsítványhivatkozást az operációsrendszer-profilt a titkos kulcsok tulajdonságában jelen kell lennie. 

A forrás tároló jelző célja hozzáférés-vezérlési lista (ACL) házirendeket, amely szerepel a felhasználó Azure Cloud Service modell érvényesítését. Ha nincs megadva a forrás-tárolóba, telepítéséhez, vagy titkos kulcsok számára kulcstároló hozzáférési jogosultságokkal nem rendelkező felhasználók lenne képes keresztül egy számítási erőforrás szolgáltató (CRP). Hozzáférés-vezérlési listák létezik még a erőforrásokat, amelyek nem léteznek.

Ha egy nem megfelelő tároló Forrásazonosítóval azonban egy érvényes kulcstároló URL-címet ad meg, hibaüzenet jelenik meg kérdezze le a művelet során.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Ha a titkos kulcsok hozzáadni egy meglévő virtuálisgép-méretezési beállítása, a titkos kulcsok beszúrta a meglévő virtuális gépekhez, vagy csak újakat? 

Az összes a virtuális gépen, előre meglévők is hozzáadja a tanúsítványokat. Ha a virtuálisgép-méretezési készlet upgradePolicy tulajdonság értéke **manuális**, a virtuális gép hozzáadta a tanúsítványt, amikor egy kézi frissítés hajt végre a virtuális Gépet.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Ha put tanúsítványok Linux virtuális gépekhez?

Tanúsítványok telepítése a Linux virtuális gépekhez, lásd: [tanúsítványok telepítése a virtuális gépek az ügyfél által felügyelt kulcstároló](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hogyan új tároló tanúsítvány hozzáadása egy új tanúsítvány objektumot?

Tároló tanúsítvány hozzáadása egy meglévő titkos kulcsot, tekintse meg a következő PowerShell-példa. Csak egy titkos objektum használja.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Mi történik a tanúsítványok, ha a virtuális gépek újból lemezképet létrehozni?

Ha Ön újból lemezképet létrehozni egy virtuális Gépet, a tanúsítványok törlődnek. Törli a teljes operációs rendszer lemez szerepkörpéldány rendszerképét. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Mi történik, ha a tanúsítvány törlése a key vault?

Ha a titkos kulcsot a key vault törlődik, és ezt követően futtatni `stop deallocate` a virtuális géphez, majd indítsa el újra azokat hibát tapasztal. A hiba oka, hogy a KSZT kell a titkos kulcsok lekérése a kulcstároló, de ez nem lehetséges. Ebben a forgatókönyvben a virtuális gép méretezési készlet modellből törölheti a tanúsítványokat. 

A CRP-összetevő nem maradnak ügyfél titkos kulcsok. Ha `stop deallocate` a virtuálisgép-méretezési csoportban lévő összes virtuális gép esetében a gyorsítótár törlődik. Ebben a forgatókönyvben kulcsait a rendszer beolvassa az a key vault.

Ez a probléma nem tapasztal, amikor, mert a titkos kulcsot az Azure Service Fabric (a bérlő egyetlen-háló modell) gyorsítótárazott másolatának kiterjesztése.
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Miért kell segítségével megadhatja a pontos helyet a tanúsítvány URL-címhez (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), a [Service Fabric-fürt biztonsági forgatókönyvek](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Az Azure Key Vault dokumentáció szerint, hogy az beszerzése titkos REST API-t a legújabb verzióját a titkos kulcsot kell visszaadnia, ha nincs megadva a verziója.
 
Módszer | URL-cím
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Cserélje le {*titkos-name*} nevű, és cserélje le {*titkos verziójú*} a titkos kulcsot szeretné beolvasni a verzióját. Előfordulhat, hogy ki kell zárni a titkos kulcs verzióját. Ebben az esetben az aktuális verzió lekérése.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Miért kell megadni a tanúsítvány verzióját, ha a Key Vault használni?

A Key Vault vonatkozó követelményt a adja meg a tanúsítvány verziója célja abba, hogy törli a jelölést, a felhasználó milyen tanúsítvány van telepítve. a virtuális gépeken.

Hozzon létre egy virtuális Gépet, és majd frissítése a kulcstartót a titkos kulcsot, ha az új tanúsítvány letöltése nem történik meg a virtuális gépekhez. De a virtuális gépek a jelek szerint hivatkozni rá, és új virtuális gépeket az új titkos kulcs beszerzése. Ennek elkerülése érdekében meg kell egy titkos verzióra hivatkozik.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A csoport több olyan tanúsítvány, a számunkra, .cer nyilvános kulcsok elosztott működik. Mi az ajánlott módszer az ezek a tanúsítványok központi telepítéséhez a virtuálisgép-méretezési beállítása?

.Cer telepítéséhez állítsa be a nyilvános kulcsokat a virtuálisgép-méretezési, létrehozhat egy .pfx-fájlt, amely csak a .cer fájlt tartalmaz. Ehhez használja `X509ContentType = Pfx`. Például töltse be a .cer fájlt C# vagy PowerShell x509Certificate2 objektumot, és majd hívja meg a metódust. 

További információkért lásd: [X509Certificate.Export metódus (X509ContentType, karakterlánc)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>A beállítás a felhasználók számára, hogy a tanúsítványok base64 karakterláncként nem jelenik meg. A legtöbb más erőforrás-szolgáltatók kell ezt a beállítást.

Sikeres tanúsítvány Base64 kódolású karakterlánc emulációjához, kibonthatja a legújabb verzióval ellátott URL-címet a Resource Manager-sablon. A következő JSON-tulajdonság az erőforrás-kezelő sablonban a következők:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Rendelkezik a JSON-objektumok a kulcstárolók tanúsítványok burkolása?

A virtuálisgép-méretezési csoportok és a virtuális gépek tanúsítványokat kell csomagolni, a JSON-objektumok. 

Alkalmazás/x-pkcs12 tartalomtípus is támogatja. Alkalmazás/x-pkcs12 használatával, lásd: [PFX-tanúsítványokat az Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Jelenleg nem támogatjuk .cer kiterjesztésű fájlokat. A .cer fájl használatához exportálása .pfx tárolók őket.



## <a name="compliance-and-security"></a>Megfelelőségi és biztonsági

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Azok a virtuálisgép-skálázási készletekben PCI-kompatibilis?

A virtuálisgép-méretezési csoportok egy vékony API-réteget alkotnak a KSZT tetején. Mindkét összetevő a számítási platform részét képezi az Azure-szolgáltatások rendszerében.

A megfelelőség szempontjából nézve a virtuálisgép-méretezési csoportok az Azure számítási platformjának alapvető részét képezik. Ezek a csoportok ugyanazt csapatot, eszközöket, folyamatokat, üzembe helyezési módszereket, biztonsági vezérlőket, igény szerinti (JIT) fordításokat, felügyeletet, riasztásokat stb. használják, mint maga a KSZT. A virtuálisgép-méretezési csoportok megfelelnek a PCI-szabványnak, mert a KSZT része a jelenlegi PCI Data Security Standard (DSS) igazolásnak.

További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Does [Azure felügyelt Szolgáltatásidentitás](https://docs.microsoft.com/azure/active-directory/msi-overview) a virtuálisgép-méretezési csoportok?

Igen. Néhány példa MSI sablonok az Azure gyors üzembe helyezési sablonokat tekintheti meg. Linux: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Bővítmények

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>A virtuálisgép-méretezési készlet bővítmény törlése

A virtuálisgép-méretezési készlet bővítmény törléséhez használja a következő PowerShell-példa:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
A bővítménynév érték található `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Egy virtuális gép méretezési sablon példa, amely integrálható a Naplóelemzési?

A virtuális gép méretezési sablon példa, amely integrálható a Naplóelemzési, lásd: a második példáját [Azure Service Fabric-fürt üzembe helyezése, és engedélyezze a megfigyelést Naplóelemzési használatával](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Bővítmények úgy tűnik, hogy a virtuálisgép-méretezési csoportok párhuzamosan futnak. Ennek hatására a saját egyéni parancsprogramok futtatására szolgáló bővítmény sikertelen lesz. Mit tehetek a javítás?

A virtuálisgép-méretezési csoportok bővítmény alkalmazás-előkészítés kapcsolatos további tudnivalókért lásd: [bővítmény alkalmazás-előkészítés az Azure virtuálisgép-méretezési csoportok](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hogyan tegye alaphelyzetbe állítani a jelszót a virtuális gépek a virtuálisgép-méretezési csoportban lévő?

A jelszó módosítása virtuális gépek esetén a méretezési készlet két fő módja van.

- A virtuális gép méretezési modellel közvetlen módosítása. A számítási API 2017-12-01 és újabb verziói.

    Frissítse a rendszergazdai hitelesítő adatokat közvetlenül a modellben méretezési készlet (például az Azure erőforrás-kezelő, a PowerShell vagy a parancssori felület használatával). A méretezési készlet nem frissített, minden új virtuális gépek után az új hitelesítő adatokkal. Meglévő virtuális gépek csak az új hitelesítő adatokkal rendelkezik, ha azok lemezképet. 

- Alaphelyzetbe állítja a jelszót a virtuális gép eléréséhez kapcsolódó kiegészítő mezőket.

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


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hogyan adni a bővítmény virtuális gépeinek a virtuálisgép-méretezési csoportban lévő?

Ha a frissítés-házirend **automatikus**, minden virtuális gép ismételt üzembe helyezéssel a sablont az új bővítmény tulajdonságai a frissíti.

Ha a frissítés-házirend **manuális**, először frissítse a bővítményt, és frissítse manuálisan a virtuális gépek összes példányán.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Ha egy meglévő virtuálisgép-méretezési csoport társított bővítmények frissítése, a meglévő virtuális gépek hatással? (Ez azt jelenti, hogy a rendszer a virtuális gépek *nem* felel meg a virtuális gép méretezési modellel?) Vagy azok figyelmen kívül hagy? Ha egy meglévő számítógép szolgáltatás-beforrott, vagy lemezképet, azok a parancsprogramok, jelenleg be van állítva a végre virtuálisgép-méretezési csoport, vagy használja a parancsprogramok, amely be lett állítva, a virtuális gép létrehozásakor?

A bővítmény-definíció a virtuálisgép-méretezési, ha a modell frissül, és a upgradePolicy tulajdonság értéke **automatikus**, hogy frissíti a virtuális gépeket. Ha a upgradePolicy tulajdonsága **manuális**, bővítmények megjelölt a modell nem megfelelő. 

Ha egy meglévő virtuális gép szolgáltatás beforrott, újraindítás jelenik meg, és vannak a bővítmények nem futtatják újra. Ha rendszerképének, például a forrás lemezkép az operációs rendszer meghajtó lecserélését is. Bármely futtató és specializációt bővítmények, például a legújabb modellből futnak.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hogyan csatlakozzon a egy virtuálisgép-méretezési állítsa be az Active Directory-tartományhoz?

A virtuálisgép-méretezési állítsa be az Active Directory (AD) tartományhoz csatlakozik egy bővítmény adhat meg. 

Egy bővítmény megadásához használja a JsonADDomainExtension tulajdonság:

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
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>A virtuálisgép-méretezési készlet bővítmény próbál telepíteni, amelyet újra kell indítani. Például: "commandToExecute": "powershell.exe - ExecutionPolicy Unrestricted Install-WindowsFeature-Name FS-Resource-Manager – IncludeManagementTools"

Ha a virtuálisgép-méretezési készlet bővítmény próbál telepíteni, amelyet újra kell indítani, használhatja az Azure Automation célállapot-konfiguráció (Automation DSC) bővítmény. Ha az operációs rendszer Windows Server 2012 R2, a Azure lekéri a Windows Management Framework (WMF) 5.0 telepítő újraindul, a, és a Folytatás a konfigurációjával. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hogyan bekapcsolni kártevőirtó a virtuálisgép-méretezési csoportban lévő?

A virtuálisgép-méretezési csoport a kártevőirtó bekapcsolásához használja a következő PowerShell-példa:

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

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Üzemeltetett egyéni parancsfájl végrehajtása olyan titkos tárfiókban kell. A parancsfájl sikeresen fut, ha a tároló nem nyilvános, de egy közös hozzáférésű Jogosultságkód (SAS) használata közben, az nem sikerül. Ez az üzenet jelenik meg: "Kötelező paraméter hiányzik az érvényes a közös hozzáférésű Jogosultságkód". Hivatkozás + SAS jól működik a helyi böngészőből.

Személyes tárfiókokban üzemeltetett egyéni parancsfájl végrehajtása védett beállítások megadása a tárfiók hívóbetűjét, a név. További információkért lásd: [egyéni parancsfájl kiterjesztése a Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Hálózat
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Ennyi az egész lehet hozzárendelni egy méretezési, a hálózati biztonsági csoport (NSG), hogy a virtuális gép hálózati adapterek összes készletében vonatkozik?

Igen. Hálózati biztonsági csoport közvetlenül egy méretezési állítja be a profil networkinterfaceconfigurations tulajdonságok szakaszának hivatkoznak rá kell alkalmazni. Példa:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hogyan egy virtuális IP-címcsere a virtuálisgép-méretezési csoportok a azonos előfizetésben és ugyanabban a régióban?

Ha két virtuálisgép-méretezési csoportok az Azure Load Balancer első-végpontok, és ugyanazt az előfizetést és régió vannak, akkor képes mindegyiknél a nyilvános IP-címek felszabadítása, és rendelje hozzá a másikra. Lásd: [virtuális IP-Címcsere: kék-zöld telepítése Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) például. Ez a késleltetés utalnak, ha az erőforrások vannak, a hálózati felszabadítása/lefoglalt szinten. A gyorsabb lehetőség egy Azure Application Gateway két háttérkészlet és útválasztási szabályainak használhat. Alternatív megoldásként az alkalmazásba sikerült működteti [Azure App service](https://azure.microsoft.com/services/app-service/) amelyek támogatást nyújt az gyors váltás átmeneti és üzemi tárhelyek között.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hogyan adjon meg egy tartományt, magánhálózati IP-címek statikus magánhálózati IP-címek hozzárendelését használandó?

Megadott alhálózatból származó IP-címek van kiválasztva. 

A kiosztási módszer a virtuális gép méretezési készlet IP-címek mindig "dinamikus", de, hogy nem jelenti azt, hogy az IP-címeket módosíthatja. Ebben az esetben "dinamikus" csak azt jelenti, hogy nincs megadva az IP-cím a PUT kérelmek. Adja meg a statikus az alhálózat-beállításokat. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hogyan telepíthető egy meglévő Azure virtuális hálózat beállítása virtuálisgép-méretezési? 

Egy meglévő Azure virtuális hálózat beállítása virtuálisgép-méretezési központi telepítéséhez lásd: [központi telepítése egy virtuális gép méretezési meglévő virtuális hálózat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Hogyan adja hozzá az első virtuális gép IP-címét a sablon kimeneti értéke virtuálisgép-méretezési?

Adja hozzá az első virtuális gép IP-címét állítsa be a sablon kimeneti virtuálisgép-méretezési, lásd: [Azure Resource Manager: Get virtulálisgép-skálázási készletekben privát IP-címek](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Méretezési készlet használható a hálózat elérését gyorsítja fel?

Igen. Gyorsított hálózatkezelés használatához állítsa enableAcceleratedNetworking igaz a méretezési a networkinterfaceconfigurations tulajdonságok beállításai. Példa:
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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hogyan konfigurálhatja a DNS-kiszolgálókat, a méretezési készlet?

Hozzon létre egy virtuálisgép-méretezési állítható be egy egyéni DNS-konfiguráció, vegye fel a dnsSettings JSON-csomagot a méretezési készletet networkinterfaceconfigurations tulajdonságok szakasz. Példa:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hogyan konfigurálható a skála állítsa be a nyilvános IP-cím hozzárendelése az egyes virtuális gépek?

Egy virtuálisgép-méretezési csoport, amely egy nyilvános IP-címet rendel az egyes virtuális gépek létrehozásához tegye a Microsoft.Compute/virtualMachineScaleSets erőforrás API-verzió 2017-03-30, és adja hozzá a _publicipaddressconfiguration_ JSON a skála csomag IP-konfigurációk szakasz beállítása. Példa:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Konfigurálhatja a méretezési több alkalmazás átjáró használható készletben?

Igen. Az erőforrás-azonosítója a számára a több Alkalmazásátjáró háttércímkészletek is hozzáadhat a _applicationGatewayBackendAddressPools_ listájában a _IP-konfigurációk_ hálózati profil állítja a skála.

## <a name="scale"></a>Méretezés

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Milyen esetben volna létrehozni egy virtuálisgép-méretezési beállítása kevesebb mint két virtuális gépek?

Egy ok arra, hogy hozzon létre egy virtuálisgép-méretezési kevesebb mint két virtuális gépek beállítása lenne a virtuálisgép-méretezési csoport rugalmas tulajdonságait. Például telepíthet egy virtuálisgép-méretezési állítsa nulla virtuális gépek definiálása az infrastruktúra nélkül költségek futtató virtuális fizet. Majd amikor készen áll a virtuális gépek telepítése, növelheti a "", a virtuálisgép-méretezési beállítása a termelési példányszámot.

Előfordulhat, hogy hozzon létre egy virtuálisgép-méretezési kevesebb mint két virtuális gépek állítsa be a másik OK, ha aggódik, kevesebb, mint a rendelkezésre állási készlet különálló virtuális gépek használatával rendelkezésre állással. Virtuálisgép-méretezési csoportok adjon meg tudja dolgozni, amelyek helyettesíthető magánháztartás számítási egység. Ez egységes a legfontosabb különbséget a virtuálisgép-méretezési csoportok és a rendelkezésre állási készletek. Sok állapot nélküli munkaterheléseket nem követik nyomon egyedi. Ha a terhelés esik, egy számítási egységhez csökkentheti, és majd vertikális felskálázás sok amikor növeli a munkaterhelés.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hogyan változtathatom meg a virtuálisgép-méretezési csoportban lévő virtuális gépek számát?

A virtuális gépek számát az Azure portálon beállítani a virtuálisgép-méretezési módosításához a virtuálisgép-méretezési állítsa be a Tulajdonságok szakaszának, kattintson a "Méretezés" panelen, és a csúszkával. Más módokon módosítása a példányok száma, lásd: [módosítása a példányok száma a virtuálisgép-méretezési csoport](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hogyan határozza meg az egyéni riasztások az egyes küszöbértékek elérésekor?

Hogyan kezeli a megadott küszöbértékek riasztások néhány beleszólása van. Például a testreszabott webhookok adhat meg. A következő webhook példája egy Resource Manager-sablon:

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

Ebben a példában egy riasztás ugrik Pagerduty.com a küszöbérték elérésekor.



## <a name="patching-and-operations"></a>Javítás és műveletek

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Hogyan hozható létre egy meglévő erőforráscsoportot beállított terjedő skálán?

Méretezési csoportok létrehozása a meglévő erőforrás csoport még nem lehetséges az Azure-portálon, de megadhat egy létező erőforráscsoportot, amikor egy méretezési telepítése Azure Resource Manager-sablonok állítható be. A méretezési készletben, Azure PowerShell vagy a parancssori felület használatával létrehozásakor egy meglévő erőforráscsoportot is megadható.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Azt áthelyezése egy másik erőforráscsoportba beállítása méretezési?

Igen, áthelyezheti méretezési erőforrások egy új előfizetés vagy az erőforráscsoportot.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hogyan való frissíteni a virtuálisgép-méretezési új lemezkép értékre? Hogyan kezelhetők a javítás?

A virtuálisgép-méretezési beállítása egy új lemezkép frissítése, valamint hogyan kezelhetők a javítás, lásd: [frissítéséhez egy virtuálisgép-méretezési csoport](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>A lemezkép-visszaállítási művelet használatával egy virtuális gép visszaállítása a kép megváltoztatása nélkül? (Ez azt jelenti, hogy kívánt egy virtuális gép visszaállítása a gyári beállításokra, nem pedig egy új lemezképen.)

Igen, a lemezkép-visszaállítási művelet használatával egy virtuális gép visszaállítása a kép megváltoztatása nélkül. Azonban ha a virtuálisgép-méretezési csoport platform lemezképre hivatkozik a `version = latest`, a virtuális gép frissíthető újabb operációsrendszer-lemezképek hívásakor `reimage`.

További információkért lásd: [virtuálisgép-méretezési csoportban lévő összes virtuális gépek kezeléséhez](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Az lehetséges méretezési csoportok integrálása az Azure Log Analytics?

Igen, a Log Analytics-bővítmény telepítése után a skála beállíthat virtuális gépeket. Íme egy Azure CLI-példa:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
A szükséges workspaceId és workspaceKey az OMS-portálon található. A áttekintése lapon kattintson a beállítások csempére. Kattintson a csatlakoztatott források fülre az oldal tetején.

Megjegyzés: Ha a méretezési _upgradePolicy_ értéke kézi, alkalmaznia kell a a bővítményt a készlet az összes virtuális gépek meghívásával beolvasott frissítés őket. A parancssori felület lenne _az vmss frissítés-példányok_.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hogyan rendszerindítási diagnosztika bekapcsolásához?

A rendszerindítási diagnosztika bekapcsolásához, először hozzon létre egy tárfiókot. Ez a JSON-tömb, majd be a virtuálisgép-méretezési csoport **virtualMachineProfile**, és a virtuálisgép-méretezési csoport frissítése:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Új virtuális gép létrehozásakor a InstanceView tulajdonság a virtuális gép számára a képernyőkép, és így tovább részleteit jeleníti meg. Például:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hogyan szerezhetek tulajdonság adatait az egyes virtuális gépek több hívás nélkül? Például hogyan kellene jelenik meg a tartalék tartomány 100 virtuális gépek mindegyikének a virtuálisgép-méretezési csoportban lévő?

Ahhoz, hogy az egyes virtuális gépek a tulajdonság adatait anélkül, hogy több hívást, hívása `ListVMInstanceViews` egy REST API végrehajtásával `GET` a következő alaposztály:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>I argumentumok adhatók át másik bővítményt egy virtuálisgép-méretezési csoportban lévő különböző virtuális gépek?

Nem, a virtuálisgép-méretezési csoportban lévő különböző virtuális gépek különböző argumentumok nem adható át. Azonban bővítmények működhet-e azok futnak, például a gép nevét a virtuális gép egyedi tulajdonságai alapján. Bővítmények is lekérdezheti példány metaadatok a http://169.254.169.254 a virtuális gép kapcsolatban további információkat.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Miért van a virtuális gép méretezési VM számítógép nevének és a virtuális gép azonosítók között lévő hézagokat? Például: 0, 1, 3...

Nincsenek a virtuális gép méretezési VM számítógép nevének és a virtuális gép azonosítók között lévő hézagokat, mert a virtuálisgép-méretezési csoport **szükségesnél több erőforrás** tulajdonság értéke az alapértelmezett érték **igaz**. Ha elhelyezésétől **igaz**, mint a kért jönnek létre további virtuális gépek. Extra virtuális Gépekért törlődnek. Ebben az esetben azt szerezhet telepítés megbízhatóságát, azonban rovására összefüggő elnevezési és összefüggő hálózati címfordítás (NAT) szabályok. 

Állíthatja ezt a tulajdonságot **hamis**. Kis virtuálisgép-méretezési csoportok, a központi telepítés megbízhatóság jelentősen ez nincs hatással.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Mi az a különbség egy virtuális Gépet egy virtuálisgép-méretezési csoportban lévő törlése és a virtuális gép felszabadítása? Ha válasszam közül történő?

A virtuálisgép-méretezési csoportban lévő virtuális gép törlése, és a virtuális gép felszabadítása közötti fő különbség, hogy `deallocate` nem törli a virtuális merevlemezek (VHD-k). Nincsenek futó társított tárolási költségek `stop deallocate`. Az egyiket a következő okok valamelyike használhatja:

- Le kívánja állítani a számítási költségek fizet, de megtartja a virtuális gépek állapotot.
- Indítsa el a virtuális gépek halmaza gyorsabban volt a virtuálisgép-méretezési csoport horizontális szeretné.
  - A forgatókönyvhöz kapcsolódó, előfordulhat, hogy létrehozta a saját automatikus skálázás-motor és a kívánt gyorsabb végpontok közötti skálán.
- A virtuálisgép-méretezési csoport, amely nem egyenlően elosztva tartalék tartományok vagy a frissítési tartományok rendelkezik. Ez lehet, mert szelektív módon törli a virtuális gépek vagy virtuális gépek elhelyezésétől után törölték. Futó `stop deallocate` követ `start` a virtuális gépen méretezési készletben egyenlően osztja el a virtuális gépek között tartalék tartományok vagy a frissítési tartományok.

