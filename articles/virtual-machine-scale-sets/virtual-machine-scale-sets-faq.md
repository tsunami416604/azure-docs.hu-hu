---
title: Azure Virtual Machine Scale Sets – gyakori kérdések
description: Válaszokat kaphat az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos leggyakoribb kérdésekre.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mimckitt
ms.openlocfilehash: 1dbc08e01b9a36b1bc80ee6b81ceb2d92ff831cc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273715"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure Virtual Machine Scale Sets – gyakori kérdések

Válaszok at kaphat az Azure-beli virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdésekre.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>A méretezési csoportokleggyakoribb kérdései

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hány virtuális gépet tartalmazhat egy méretezési csoport?

Egy méretezési készlet rendelkezhet 0 és 1000 virtuális gép platformrendszerképek alapján, vagy 0 és 600 virtuális gép egyéni lemezképek alapján.

### <a name="are-data-disks-supported-within-scale-sets"></a>Támogatott az adatlemezek használata a méretezési csoportokon belül?

Igen. A méretezési csoportok meghatározhatnak egy csatlakoztatott adatlemezekből álló konfigurációt, amely a csoport összes virtuális gépére érvényes. További információ: [Azure-beli méretezési csoportok és csatlakoztatott adatlemezek](virtual-machine-scale-sets-attached-disks.md). Egyéb adattárolási lehetőségek:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Azure-adatszolgáltatás (például Azure-táblák, Azure-blobok)
* Külső adatszolgáltatás (például távoli adatbázis)

### <a name="which-azure-regions-support-scale-sets"></a>Mely Azure-régiók támogatják a méretezési csoportokat?

Mindegyik régió támogatja a méretezési csoportokat.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hogyan lehet egyéni rendszerképekből méretezési csoportot létrehozni?

Hozzon létre és rögzítsen egy virtuális géplemezképet, majd használja azt a méretezési csoport forrásaként. Az egyéni virtuálisgép-lemezkép létrehozásáról és használatáról szóló oktatóanyaghoz használhatja az [Azure CLI-t](tutorial-use-custom-image-cli.md) vagy az [Azure PowerShellt](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Ha a méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például: 20, 21, 22). A virtuális gépek a tartalék és frissítési tartományok között oszlanak el.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Ha több bővítményt használok egy méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

Igen, használhatja a méretezési készlet [bővítmény szekvenálását.](virtual-machine-scale-sets-extension-sequencing.md)

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Használhatok virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

A regionális (nem zónaszintű) méretezési csoport *elhelyezési csoportokat*használ, amelyek öt tartalék tartománnyal és öt frissítési tartománnyal implicit rendelkezésre állási csoportként működnek. A 100-nál több virtuális gépből álló méretezési csoportok több elhelyezési csoportra terjednek ki. További információ az elhelyezési csoportokról: [Nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció egy rendelkezésre állási csoportba helyezi a vezérlő csomópont virtuális gépeit (ezek gyakran igényelnek egyéni konfigurálást), és a méretezési csoportba helyezi az adatcsomópontokat.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>A méretezési csoportok működnek az Azure rendelkezésre állási zónáival?

Igen! További információt a [méretezési zóna dokumentuma című témakörben talál.](./virtual-machine-scale-sets-use-availability-zones.md)


## <a name="autoscale"></a>Automatikus méretezés

### <a name="what-are-best-practices-for-azure-autoscale"></a>Mik az ajánlott eljárások az Azure automatikus skálázáshoz?

Az automatikus skálázás ajánlott eljárásokért olvassa el [az automatikus virtualgépek automatikus skálázásának ajánlott eljárások című témakört.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Hol találhatom meg a gazdagépalapú metrikákat használó automatikus skálázás metrikaneveit?

A gazdagépalapú metrikákat használó automatikus skálázás metrikaneveit lásd: [Támogatott metrikák az Azure Monitorhasználatával](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/)című témakörben.

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Vannak-e példák az Azure Service Bus-témakör és a várólista hossza alapján automatikus skálázásra?

Igen. Példák az Azure Service Bus-témakörés a várólista hossza alapján automatikus skálázás, lásd: [Azure Monitor automatikus skálázás gyakori metrikák.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

Service Bus-várólista esetén használja a következő JSON-t:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Tárolóvárólista esetén használja a következő JSON-t:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Példaértékek lecserélése az erőforrás egységes erőforrás-azonosítóival (URI-k).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Automatikus skálázást kell használnom gazdagép-alapú metrikák vagy diagnosztikai bővítmény használatával?

Létrehozhat egy automatikus skálázási beállítást egy virtuális gépen a gazdagépszintű metrikák vagy a vendég operációsrendszer-alapú metrikák használatához.

A támogatott metrikák listáját az [Azure Monitor automatikus skálázása gyakori metrikák.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics)

A virtuálisgép-méretezési csoportok teljes mintáját a [Speciális automatikus skálázási konfiguráció a virtuálisgép-méretezési készletek Erőforrás-kezelő sablonjainak használatával olvassa el.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)

A minta a gazdagépszintű CPU-metrikát és egy üzenetszám-metrikát használ.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hogyan állíthatok be riasztási szabályokat egy virtuálisgép-méretezési csoporton?

A PowerShell vagy az Azure CLI segítségével riasztásokat hozhat létre a virtuálisgép-méretezési csoportok metrikákhoz. További információ: [Azure Monitor PowerShell-gyorsindítási minták](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) és [az Azure Monitor platformfüggetlen CLI-gyorsindítási minták.](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)

A virtuálisgép-méretezési csoport TargetResourceId azonosítója a következőképpen néz ki:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Bármelyik virtuális gép teljesítményszámlálója a metrika riasztás beállításaként. További információ: [Vendég operációs rendszer metrikák resource manager-alapú Windows-virtuális gépek](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) és [a vendég operációs rendszer metrikák Linux virtuális gépek](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) az Azure Monitor automatikus [skálázás gyakori metrikák](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) cikkben.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hogyan állíthatom be az automatikus skálázást egy PowerShell használatával beállított virtuálisgép-méretezésen?

Ha a PowerShell használatával automatikus méretezést szeretne beállítani egy virtuálisgép-méretezési skálán, olvassa el [a virtuálisgép-méretezési csoport automatikus méretezése.](tutorial-autoscale-powershell.md) Az automatikus skálázást az [Azure CLI](tutorial-autoscale-cli.md) és az [Azure sablonokkal](tutorial-autoscale-template.md) is konfigurálhatja


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Ha leállítottam (felszabadította) a virtuális gép, az, hogy a virtuális gép egy automatikus skálázási művelet részeként indult?

Nem. Ha az automatikus skálázási szabályok további virtuálisgép-példányokat igényelnek egy méretezési csoport részeként, egy új virtuálisgép-példány jön létre. A leállított (felszabadított) virtuálisgép-példányok nem automatikus skálázási esemény részeként indulnak el. Azonban ezek a leállított (felszabadított) virtuális gépek törölhetők részeként egy automatikus skálázási esemény, amely skálázódik a példányok száma, ugyanúgy, hogy bármely virtuálisgép-példány törölhető a virtuálisgép-példány azonosítója szerint.



## <a name="certificates"></a>Tanúsítványok

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hogyan tudok biztonságosan szállítani egy tanúsítványt a virtuális gépre?

A tanúsítvány biztonságos szállításához a virtuális gép, telepítheti a vevői tanúsítvány közvetlenül a Windows tanúsítványtárolóba az ügyfél key vault.

Használja a következő JSON-t:

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

A kód támogatja a Windows és a Linux.

További információt a [Virtuálisgép-méretezési csoport létrehozása vagy frissítése](https://msdn.microsoft.com/library/mt589035.aspx)című témakörben talál.


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hogyan használhatom az Azure Service Fabric-fürtökhöz kiépített önaláírt tanúsítványokat?
A legújabb példában használja a következő azure CLI utasítást az azure shellben, olvassa el a Service Fabrics CLI modul példa dokumentációját, amelyet a rendszer a rendszer stdout-ra nyomtat:

```azurecli
az sf cluster create -h
```

Az önaláírt tanúsítványok nem használhatók a hitelesítésszolgáltató által biztosított elosztott megbízhatósági kapcsolatra, és nem használhatók vállalati termelési megoldások üzemeltetésére szánt Service Fabric-fürthöz; további Service Fabric-biztonsági útmutatásért tekintse át az [Azure Service Fabric biztonsági gyakorlati tanácsait](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) és a Service Fabric [fürtbiztonsági forgatókönyveit.](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Megadhatok egy SSH-kulcspárt, amelyet az SSH-hitelesítéshez használhat egy Erőforrás-kezelő sablonból származó Linux virtuálisgép-méretezési készlettel?

Igen. Az **osProfile** REST API-ja hasonló a szabványos VM REST API-hoz.

Az **osProfile** felvétele a sablonba:

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

Ez a JSON-blokk ebben az [Azure-gyorsindítási sablonban](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)használatos.

További információt a [Virtuálisgép-méretezési csoport létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)című témakörben talál.

### <a name="how-do-i-remove-deprecated-certificates"></a>Hogyan távolíthatom el az elavult tanúsítványokat?

Elavult tanúsítványok eltávolításához távolítsa el a régi tanúsítványt a tároló tanúsítványok listájáról. Hagyja a listában az összes olyan tanúsítványt, amelyet a számítógépen szeretne hagyni. Ez nem távolítja el a tanúsítványt az összes virtuális gépről. Emellett nem adja hozzá a tanúsítványt a virtuális gép méretezési csoportban létrehozott új virtuális gépekhez.

Ha el szeretné távolítani a tanúsítványt a meglévő virtuális gépekről, egy egyéni parancsfájl-bővítmény segítségével manuálisan távolítsa el a tanúsítványokat a tanúsítványtárolóból.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hogyan fecskendezhetek be egy meglévő SSH nyilvános kulcsot a virtuálisgép-méretezési készlet SSH-rétegbe a kiépítés során?

Ha a virtuális gépek csak egy nyilvános SSH-kulcs, nem kell a nyilvános kulcsokat a Key Vaultban. A nyilvános kulcsok nem titkosak.

Az SSH nyilvános kulcsokat egyszerű szövegként biztosíthatja, amikor linuxos virtuális géplétrehozása kor:

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
}
```

linuxConfiguration elem neve | Kötelező | Típus | Leírás
--- | --- | --- | ---
Ssh | Nem | Gyűjtemény | A Linux operációs rendszer SSH-kulcskonfigurációjának megadása
path | Igen | Sztring | Megadja azt a Linux fájlelérési utat, ahol az SSH-kulcsoknak vagy tanúsítványnak el kell helyezkednie.
kulcsadatok | Igen | Sztring | Base64 kódolású SSH nyilvános kulcsot ad meg

Például tekintse meg [a 101-vm-sshkey GitHub rövid útmutató sablont.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Amikor egynél több tanúsítvány hozzáadása után futok `Update-AzVmss` ugyanabból a kulcstartóból, a következő üzenet jelenik meg:

>Update-AzVmss: A listatitkos lista a /subscriptions/\<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev ismétlődő példányait tartalmazza, amely nem engedélyezett.

Ez akkor fordulhat elő, ha megpróbálja újra hozzáadni ugyanazt a tárolót, ahelyett, hogy egy új tároló tanúsítványt használ nanca a meglévő forrástárolóhoz. A `Add-AzVmssSecret` parancs nem működik megfelelően, ha további titkos kulcsokat ad hozzá.

Ha további titkos kulcsokat szeretne hozzáadni ugyanabból a key vaultból, frissítse a $vmss.properties.osProfile.secrets[0].vaultCertificates listát.

A várt beviteli struktúráról a [Virtuálisgép-készlet létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt589035.aspx)című témakörben található.

Keresse meg a titkos kulcsot a virtuális gép méretezési készlet objektum, amely a key vaultban található. Ezután adja hozzá a tanúsítványhivatkozást (az URL-címet és a titkos tároló nevét) a tárolóhoz társított listához.

> [!NOTE]
> Jelenleg nem távolíthatja el a tanúsítványokat a virtuális gépekről a virtuálisgép-méretezési csoport API használatával.
>

Az új virtuális gépek nem rendelkeznek a régi tanúsítvánnyal. Azonban a tanúsítvánnyal rendelkező és már üzembe helyezett virtuális gépek a régi tanúsítvánnyal rendelkeznek.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Leküldéses tanúsítványok a virtuális gép méretezési készlet megadása nélkül a jelszót, ha a tanúsítvány a titkos tárolóban?

Nem kell a parancsfájlok jelszavát kódolni. A központi telepítési parancsfájl futtatásához használt engedélyekkel dinamikusan lekérheti a jelszavakat. Ha olyan parancsfájllal rendelkezik, amely áthelyezi a tanúsítványt a titkos tároló kulcstartójából, a titkos tároló `get certificate` parancs a .pfx fájl jelszavát is kiadja.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hogyan működik a VirtualMachineProfile.osProfile a virtualMachine.osProfile a virtuális gép méretezési készlete működik? Miért van szükségem a sourceVault értékre, ha meg kell adnom egy tanúsítvány abszolút URI-ját a certificateUrl tulajdonság használatával?

A Windows távfelügyeleti (WinRM) tanúsítványhivatkozásnak meg kell jelennie az operációsrendszer-profil Betanis (Betsi) tulajdonságában.

A forrástároló jelzésének célja a felhasználó Azure Cloud Service-modelljében létező hozzáférés-vezérlési lista (ACL) házirendek kényszerítése. Ha a forrástároló nincs megadva, a felhasználók, akik nem rendelkeznek engedéllyel a kulcstartóba való telepítéshez vagy a kulcstárolóhoz való hozzáféréshez, számítási erőforrás-szolgáltatón (CRP) keresztül férhetnek hozzá. Az ACL-ok még a nem létező erőforrásokhoz is léteznek.

Ha helytelen forrástároló-azonosítót, de érvényes kulcstároló URL-címet ad meg, a rendszer hibát jelez a művelet lekérdezésekor.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Ha titkos kulcsokat adok hozzá egy meglévő virtuálisgép-méretezési csoporthoz, a meglévő virtuális gépekbe injektált titkos kulcsok, vagy csak újakba kerülnek?

A tanúsítványok az összes virtuális géphez hozzáadódnak, még a már meglévőkhez is. Ha a virtuálisgép méretezési csoport upgradePolicy tulajdonság manual ( **manuális**) beállítással érhető el, a tanúsítvány a virtuális géphez kerül, amikor manuális frissítést hajt végre a virtuális gépen.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Hol helyezhetek el tanúsítványokat a Linux virtuális gépekhez?

A Linux-virtuális gépek tanúsítványainak központi telepítéséről a [Tanúsítványok telepítése a virtuális gépekre egy ügyfél által felügyelt kulcstartóból.](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hogyan vehetek fel új tárolótanúsítványt egy új tanúsítványobjektumba?

Egy tároló tanúsítvány hozzáadása egy meglévő titkos kulcsot, tekintse meg a következő PowerShell-példa. Csak egy titkos objektumot használjon.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Mi történik a tanúsítványokkal, ha újrafeltud szidegy virtuális gép?

Ha újraegy virtuális gép, tanúsítványok törlődnek. A reimaging törli a teljes operációsrendszer-lemezt.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Mi történik, ha töröl egy tanúsítványt a key vaultból?

Ha a titkos kulcs törlődik a key `stop deallocate` vaultból, és majd fut az összes virtuális gépek, majd indítsa el őket újra, hibát tapasztal. A hiba azért fordul elő, mert a CRP-nek le kell kérnie a titkos kulcsokat a key vaultból, de nem. Ebben az esetben törölheti a tanúsítványokat a virtuálisgép méretezési csoport modell.

A CRP-összetevő nem őrizi meg az ügyféltitkokat. Ha a `stop deallocate` virtuálisgép-méretezési csoportban lévő összes virtuális géphez futtatja, a gyorsítótár törlődik. Ebben a forgatókönyvben a titkos kulcsok a key vaultból kerülnek beolvasásra.

Ez a probléma nem merül fel horizontális felskálázáskor, mert az Azure Service Fabric titkos titkának gyorsítótárazott másolata van (az egyhálós bérlői modellben).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Miért kell megadnom a tanúsítvány verzióját a Key Vault használatakor?

A tanúsítványverzió megadására vonatkozó key vault-követelmény célja, hogy egyértelművé tegye a felhasználó számára, hogy milyen tanúsítvány van telepítve a virtuális gépeken.

Ha létrehoz egy virtuális gépet, és majd frissíti a titkos kulcsot a key vaultban, az új tanúsítvány nem töltődik le a virtuális gépekre. De a virtuális gépek úgy tűnik, hogy hivatkoznak rá, és az új virtuális gépek az új titkos kulcsot kap. Ennek elkerülése érdekében egy titkos verzióra kell hivatkoznia.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A csapatom számos olyan tanúsítvánnyal dolgozik, amelyeket .cer nyilvános kulcsként osztanak ki. Mi a ajánlott megközelítés a tanúsítványok virtuálisgép-méretezési csoportra való üzembe helyezéséhez?

Ha a .cer nyilvános kulcsokat egy virtuálisgép-méretezési csoportba szeretné telepíteni, létrehozhat egy .pfx fájlt, amely csak .cer fájlokat tartalmaz. Ehhez használja a `X509ContentType = Pfx`használatát. Töltse be például a .cer fájlt x509Certificate2 objektumként a C# vagy a PowerShell fájlba, majd hívja meg a metódust.

További információ: [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Hogyan tudok átmenni a tanúsítványokat base64 karakterláncként?

Ha a tanúsítványt base64-karakterláncként szeretné emulálni, kibonthatja a legújabb verziójú URL-címet egy Erőforrás-kezelő sablonban. Az erőforrás-kezelő sablonba a következő JSON-tulajdonság szerepeljenek:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>A tanúsítványokat JSON-objektumokba kell burkolnom a kulcstartókban?

A virtuálisgép-méretezési csoportokban és a virtuális gépekben a tanúsítványokat JSON-objektumokba kell csomagolni.

Támogatjuk a tartalomtípus alkalmazást/x-pkcs12.

Jelenleg nem támogatjuk a .cer fájlokat. A .cer fájlok használatához exportálja őket .pfx tárolókba.



## <a name="compliance-and-security"></a>Megfelelőség és biztonság

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>A virtuálisgép-méretezési készletek PCI-kompatibilisek?

A virtuálisgép-méretezési csoportok egy vékony API-réteget alkotnak a KSZT tetején. Mindkét összetevő a számítási platform részét képezi az Azure-szolgáltatások rendszerében.

A megfelelőség szempontjából nézve a virtuálisgép-méretezési csoportok az Azure számítási platformjának alapvető részét képezik. Ezek a csoportok ugyanazt csapatot, eszközöket, folyamatokat, üzembe helyezési módszereket, biztonsági vezérlőket, igény szerinti (JIT) fordításokat, felügyeletet, riasztásokat stb. használják, mint maga a KSZT. A virtuálisgép-méretezési csoportok megfelelnek a PCI-szabványnak, mert a KSZT része a jelenlegi PCI Data Security Standard (DSS) igazolásnak.

További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Az [Azure-erőforrások felügyelt identitásai](https://docs.microsoft.com/azure/active-directory/msi-overview) virtuálisgép-méretezési csoportokkal működnek?

Igen. Néhány példa MSI-sablont láthat a [Linuxra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) és Windowsra készült Azure gyorsindítási [sablonjaiban.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)

## <a name="deleting"></a>Törlés 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>A virtuálisgép-méretezési példányokon beállított zárolások belesznek tartva a példányok törlésekor?

Az Azure Portalon több példány kijelölésével törölheti az egyes példányokat vagy tömeges törlést. Ha egyetlen olyan példányt próbál törölni, amelynek zárolása van érvényben, a zárolás tiszteletben marad, és nem tudja törölni a példányt. Ha azonban tömegesen választ ki több példányt, és ezek közül bármelyik példány zárolása van érvényben, a zárolás(oka)t nem tartják be, és az összes kiválasztott példány törlődik. 
 
Az Azure CLI-ben csak egy adott példány törlésére van lehetősége. Ha egyetlen olyan példányt próbál törölni, amelynek zárolása van érvényben, a zárolás tiszteletben marad, és nem tudja törölni a példányt. 

## <a name="extensions"></a>Bővítmények

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hogyan törölhetek egy virtuálisgép-méretezési csoport bővítményét?

Virtuálisgép-méretezési csoport bővítményének törléséhez használja a következő PowerShell-példát:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

A bővítményNév értékét a `$vmss`ban találja.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Van egy virtuálisgép-méretezési sablon példa, amely integrálja az Azure Monitor naplók?

Az Azure Monitor naplóival integrálható virtuálisgép-méretezési csoport sablonpéldáját lásd az Azure Service Fabric-fürt telepítése című témakörben, amely lehetővé teszi a [figyelést az Azure Monitor-naplók használatával.](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hogyan adhatok hozzá egy bővítményt a virtuális gép méretezési csoportában lévő összes virtuális géphez?

Ha a frissítési házirend **automatikus,** a sablon újratelepítése az új bővítmény tulajdonságokkal frissíti az összes virtuális gépet.

Ha a frissítési házirend **manuálisra**van állítva, először frissítse a bővítményt, majd manuálisan frissítse a virtuális gépek összes példányát.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Ha egy meglévő virtuálisgép-méretezési készlethez társított bővítmények frissülnek, a meglévő virtuális gépek érintettek?

Ha a bővítmény definíciója a virtuálisgép méretezési csoport modell frissül, és a upgradePolicy tulajdonság **automatikus,** frissíti a virtuális gépeket. Ha a upgradePolicy tulajdonság **manuálisra**van állítva, a bővítmények nem egyeznek meg a modellel.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>A bővítmények újra futnak, ha egy meglévő gép szervizelt vagy újraképeződik?

Ha egy meglévő virtuális gép szolgáltatás által behevélett, újraindításként jelenik meg, és a bővítmények nem futnak újra. Ha egy virtuális gép újra, a folyamat hasonló helyett az operációs rendszer meghajtóját a forráskép. A legújabb modell minden specializációja, például a bővítmények, újra futnak.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hogyan csatlakozhatok egy Active Directory-tartományhoz beállított virtuálisgép-méretezéshez?

Ha egy Active Directory (AD) tartományhoz beállított virtuálisgép-méretezési skálát szeretne csatlakoztatni, definiálhat egy bővítményt.

Bővítmény definiálásához használja a JsonADDomainExtension tulajdonságot:

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Saját virtuális gép méretezési készlet kiterjesztés próbál telepíteni valamit, ami újraindítást igényel.

Ha a virtuális gép méretezési csoport bővítménye megpróbál telepíteni valamit, amely újraindítást igényel, használhatja az Azure Automation kívánt állapotkonfiguráció (Automation DSC) bővítményt. Ha az operációs rendszer Windows Server 2012 R2, az Azure lekéri a Windows Management Framework (WMF) 5.0 beállítását, újraindul, majd folytatja a konfigurációt.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hogyan kapcsolhatom be a kártevőirtót a virtuálisgép-méretezési csoportban?

A kártevőirtó bekapcsolásához a virtuális gép méretezési csoport, használja a következő PowerShell példa:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hogyan futtathatok egyéni parancsfájlt, amely egy privát tárfiókban található?

Privát tárfiókban tárolt egyéni parancsfájl végrehajtásához állítsa be a védett beállításokat a tárfiók kulcsával és nevével. További információt az [Egyéni parancsfájl-bővítmény című](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)témakörben talál.

## <a name="passwords"></a>Jelszavak

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hogyan állíthatom vissza a virtuális gépek jelszavát a virtuális gép méretezési készletében?

A virtuális gépek jelszavának módosítása két fő módon, méretezési csoportokban.

- Módosítsa a virtuálisgép méretezési csoport modelljét közvetlenül. Api 2017-12-01 és újabb api-val érhető el.

    Frissítse a rendszergazdai hitelesítő adatokat közvetlenül a méretezési csoport modell (például az Azure Resource Explorer, powershell vagy CLI használatával). A méretezési készlet frissítése után az összes új virtuális gép rendelkezik az új hitelesítő adatokkal. A meglévő virtuális gépek csak akkor rendelkeznek az új hitelesítő adatokkal, ha újralevannak mévülve.

- Állítsa alaphelyzetbe a jelszót a virtuális gép hozzáférési bővítményei használatával.

    Használja a következő PowerShell-példát:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Hálózat

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Lehet-e hozzárendelni egy hálózati biztonsági csoportot (NSG) egy méretezési csoporthoz, hogy az a készlet összes virtuálisgép-hálózati adapterére vonatkozzon?

Igen. A hálózati biztonsági csoport közvetlenül alkalmazható egy méretezési csoportra, ha hivatkozik rá a hálózati profil NetworkInterfaceConfigurations szakaszában. Példa:

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
                            },
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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hogyan tehetek egy VIP-csere virtuálisgép-méretezési készletek ugyanabban az előfizetésben és ugyanabban a régióban?

Ha két virtuálisgép-méretezési készlettel rendelkezik az Azure Load Balancer előtér-kiszolgálókkal, és ugyanabban az előfizetésben és régióban vannak, felszabadíthatja a nyilvános IP-címeket mindegyikből, és hozzárendelheti a másikhoz. Lásd: [VIP Swap: Kék-zöld üzembe helyezés](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) az Azure Resource Manager például. Ez azonban késleltetést jelent, mivel az erőforrásokat a hálózat szintjén osztják fel/osztják fel. Gyorsabb megoldás az Azure Application Gateway két háttérkészlettel és egy útválasztási szabály használatával. Másik lehetőségként üzemeltetheti az alkalmazást az [Azure App szolgáltatással,](https://azure.microsoft.com/services/app-service/) amely támogatja az átmeneti és az éles tárolóhelyek közötti gyors váltást.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hogyan adhatok meg egy privát IP-címtartományt a statikus privát IP-címek kiosztásához?

Az IP-címek a megadott alhálózatból vannak kiválasztva.

A virtuálisgép-méretezési készlet IP-címeinek foglalási módja mindig "dinamikus", de ez nem jelenti azt, hogy ezek az IP-címek változhatnak. Ebben az esetben a "dinamikus" csak azt jelenti, hogy nem adja meg az IP-címet egy PUT-kérelemben. Adja meg a statikus halmazt az alhálózat használatával.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hogyan telepíthetek egy virtuálisgép-méretezési készletet egy meglévő Azure virtuális hálózatra?

Ha egy meglévő Azure-beli virtuális hálózatra beállított virtuálisgép-méretezési csoporthoz szeretne telepíteni, [olvassa el a Virtuálisgép-méretezési csoport telepítése meglévő virtuális hálózatra beállítását.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Használhatok méretezési csoportokat a Gyorsított hálózatkezeléshez?

Igen. A gyorsított hálózatkezelés használatához állítsa az enableAcceleratedNetworking beállítást true értékre a méretezési csoport networkInterfaceConfigurations beállításaiban. Példa:

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
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hogyan konfigurálhatom a méretezési csoport által használt DNS-kiszolgálókat?

Ha egyéni DNS-konfigurációval szeretne virtuálisgép-méretezési csoportot létrehozni, adjon hozzá egy dnsSettings JSON csomagot a scale set networkInterfaceConfigurations szakaszhoz. Példa:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hogyan konfigurálhatok egy méretezési csoportot, amely nyilvános IP-címet rendel az egyes virtuális gépekhez?

Ha olyan virtuálisgép-méretezési csoportot szeretne létrehozni, amely nyilvános IP-címet rendel az egyes virtuális gépekhez, győződjön meg arról, hogy a Microsoft.Compute/virtualMachineScaleSets erőforrás API-verziója 2017-03-30, és adjon hozzá egy _nyilvános ipaddressconfiguration_ JSON-csomagot a méretezési csoport ipConfigurations szakaszához. Példa:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Konfigurálhatok egy méretezési csoportot, hogy több alkalmazásátjáróval működjön?

Igen. Több Application Gateway-háttérrendszer-címkészlet erőforrásazonosítóit hozzáadhatja az _applicationGatewayBackendAddressPools_ listához a méretezési csoport hálózati profiljának _ipConfigurations_ szakaszában.

## <a name="scale"></a>Méretezés

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Milyen esetben hoznék létre egy virtuális gép méretezési készletkevesebb, mint két virtuális gép?

Az egyik ok, hogy hozzon létre egy virtuális gép méretezési készlet kevesebb, mint két virtuális gép lenne a virtuális gép méretezési csoport rugalmas tulajdonságainak használata. Például üzembe helyezhet egy virtuálisgép-méretezési készletet nulla virtuális géppel az infrastruktúra meghatározásához a virtuális gépek üzemeltetési költségeinek megfizetése nélkül. Ezután, ha készen áll a virtuális gépek üzembe helyezésére, növelje a "kapacitást" a virtuális gép méretezési készletaz éles példányok száma.

Egy másik ok, amiért előfordulhat, hogy hozzon létre egy virtuális gép méretezési csoport kevesebb, mint két virtuális gép, ha kevésbé érdekli a rendelkezésre állási, mint egy rendelkezésre állási készlet diszkrét virtuális gépek használatával. A virtuálisgép-méretezési készletek lekicsinyítést biztosítanak a helyettesíthető, nem differenciált számítási egységekkel. Ez az egységesség a virtuálisgép-méretezési csoportok és a rendelkezésre állási csoportok kulcsfontosságú különbsége. Számos állapot nélküli számítási feladatok nem követi nyomon az egyes egységek. Ha a munkaterhelés csökken, egy számítási egységre skálázható, majd a munkaterhelés növekedésével akár többre is felskálázható.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hogyan módosíthatom a virtuális gép méretezési csoportban lévő virtuális gépek számát?

Ha módosítani szeretné a virtuális gépek számát egy virtuális gép méretezési készletében az Azure Portalon, a virtuálisgép méretezési készlet tulajdonságai szakaszból kattintson a "Méretezés" panelre, és használja a csúszkát.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hogyan definiálhatok egyéni riasztásokat bizonyos küszöbértékek eléréséhez?

Némi rugalmasságot biztosít a megadott küszöbértékek riasztások kezelésének módjában. Például megadhatja a testreszabott webhookokat. A következő webhook-példa egy Erőforrás-kezelő sablonból származik:

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
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
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
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Javítások és műveletek

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Létrehozhatok méretezési csoportot egy meglévő erőforráscsoportban?

Igen, létrehozhat egy méretezési csoportot egy meglévő erőforráscsoportban.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Áthelyezhetek egy méretezési csoportot egy másik erőforráscsoportba?

Igen, áthelyezheti a méretezési csoport erőforrásait egy új előfizetésbe vagy erőforráscsoportba.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hogyan frissíthetem a virtuálisgép-méretezési készletemet egy új lemezképre? Hogyan kezelhetem a javítást?

A virtuálisgép-méretezési csoport új lemezképre való frissítéséhez és a javítás kezeléséhez olvassa el [a Virtuálisgép-méretezési csoport frissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Használhatom az újraképzési műveletet a virtuális gép alaphelyzetbe állításához a lemezkép módosítása nélkül? (Ez azt, azt akarom, hogy állítsa vissza a virtuális gép a gyári beállításokat, nem pedig egy új képet.)

Igen, az újraképzési művelet segítségével alaphelyzetbe állíthatja a virtuális gép a lemezkép módosítása nélkül. Ha azonban a virtuálisgép-méretezési csoport `version = latest`egy platformrendszerképre hivatkozik, a virtuális `reimage`gép egy későbbi operációsrendszer-lemezképre frissíthet híváskor.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Integrálható a méretezési csoportok az Azure Monitor-naplókkal?

Igen, az Azure Monitor bővítmény telepítésével a méretezési készlet virtuális gépeken. Íme egy Azure CLI példa:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

A szükséges munkaterület-azonosítót és munkaterületkulcsot az Azure Portal Log Analytics-munkaterületében találhatja meg. Az Áttekintés lapon kattintson a Beállítások csempére. Kattintson a felül található Csatlakoztatott források fülre.

> [!NOTE]
> Ha a méretezési készlet _upgradePolicy_ beállítása Manual, a bővítményt kell alkalmaznia a készlet összes virtuális gépére frissítés hívásával. A CLI-ben ez az _vmss frissítési példány._

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hogyan kapcsolhatom be a rendszerindítási diagnosztikát?

A rendszerindítási diagnosztika bekapcsolásához először hozzon létre egy tárfiókot. Ezután helyezze ezt a JSON-blokkot a virtuális gép méretezési készletébe **virtualMachineProfile**, és frissítse a virtuális gép méretezési készlet:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Amikor egy új virtuális gép jön létre, a Virtuálisgép InstanceView tulajdonsága megjeleníti a képernyőkép részleteit, és így tovább. Például:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hogyan kaphatok tulajdonságinformációkat az egyes virtuális gépekhez több hívás nélkül? Például hogyan szeretném beszerezni a tartalék tartományban a 100 virtuális gép a virtuális gép méretezési csoportban?

Ha az egyes virtuális gépek tulajdonságadatait több hívás `ListVMInstanceViews` nélkül szeretné `GET` lekérni, a REST API-t a következő erőforrás-URI-n teheti meg:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Átadhatok különböző bővítményargumentumokat a virtuális gép méretezési készletében lévő különböző virtuális gépeknek?

Nem, nem adhat át különböző bővítményargumentumokat a virtuálisgép-méretezési csoport különböző virtuális gépeinek. Azonban a bővítmények működhetnek a virtuális gép, amelyen futnak, például a gép neve alapján. A bővítmények is lekérdezhetik http://169.254.169.254 a példányok metaadatait, hogy további információkat kapjon a virtuális gépről.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Miért vannak hézagok a virtuálisgép-méretezési készlet virtuálisgép-nevei és a virtuálisgép-azonosítók között? Például: 0, 1, 3...

Hézagok vannak a virtuálisgép-méretezési készlet virtuálisgép-nevei és a virtuálisgép-azonosítók között, mivel a virtuálisgép-méretezési készlet **overprovision** tulajdonsága a **true**alapértelmezett értékére van állítva. Ha a túlterhelés **értéke igaz,** a kértnél több virtuális gép jön létre. Ezután az extra virtuális gépek törlődnek. Ebben az esetben nagyobb üzembe helyezési megbízhatóságot kap, de az összefüggő elnevezési és összefüggő hálózati címfordítási (NAT) szabályok rovására.

Ezt a tulajdonságot hamis értékűre **állíthatja.** A kis virtuálisgép-méretezési csoportok esetében ez nem befolyásolja jelentősen a központi telepítés megbízhatóságát.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Mi a különbség a virtuális gép törlése egy virtuális gép méretezési csoportban, és a virtuális gép hozzárendelése között? Mikor válasszak egyet a másik kal szemben?

A virtuális gép virtuális gép méretezési csoportban való törlése és a virtuális `deallocate` gép hozzárendelése közötti fő különbség az, hogy nem törli a virtuális merevlemezeket (VD-k). A futással `stop deallocate`kapcsolatban tárolási költségek merülnek fel. Az egyiket vagy a másikat a következő okok valamelyike érdekében használhatja:

- Le szeretné állítani a számítási költségek kifizetését, de meg szeretné tartani a virtuális gépek lemezállapotát.
- A virtuális gépek készletét gyorsabban szeretné elindítani, mint egy virtuálisgép-méretezési csoport.
  - Ehhez a forgatókönyvhöz kapcsolódóan előfordulhat, hogy saját automatikus skálázási motort hozott létre, és gyorsabb, végpontok között skálázást szeretne.
- Van egy virtuálisgép-méretezési készlet, amely egyenlőtlenül oszlik meg a tartalék tartományok vagy a tartományok frissítése között. Ennek oka lehet, hogy szelektíven törölt virtuális gépeket, vagy mert a virtuális gépek et a túlterhelés után törölték. Futás, `stop deallocate` `start` majd a virtuális gép méretezési készlet egyenletesen osztja el a virtuális gépek között tartalék tartományok vagy frissítési tartományok.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hogyan készíthetek pillanatképet egy virtuálisgép-méretezési csoport példányáról?
Hozzon létre egy pillanatképet egy virtuálisgép-méretezési csoport egy példányát.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Felügyelt lemez létrehozása a pillanatképből.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
