---
title: Egyéni adatok és Azure virtuális gépek
description: Részletek az egyéni adatok és a Cloud-Init Azure virtuális gépeken való használatáról
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109627"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Egyéni adatok és Cloud-Init az Azure virtuális gépeken

## <a name="what-is-custom-data"></a>Mi az egyéni adatok?

Az ügyfelek gyakran kérdezik, hogyan adhatnak be egy parancsfájlt vagy más metaadatokat a Microsoft Azure virtuális gépbe a rendelkezésre állás idején.  Más felhőkben ezt a fogalmat gyakran felhasználói adatoknak nevezik.  A Microsoft Azure-ban van egy hasonló funkció, az úgynevezett egyéni adatok. 

Az egyéni adatok csak az első rendszerindítás/kezdeti beállítás során érhetők el a virtuális gép számára, ezt nevezzük "kiépítésnek". A kiépítés az a folyamat, ahol a vm-létrehozási paraméterek (például állomásnév, felhasználónév, jelszó, tanúsítványok, egyéni adatok, kulcsok stb.) elérhetők a virtuális gép számára, és egy kiépítési ügynök feldolgozza őket, például a [Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) és a [felhőalapú init.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>Egyéni adatok átadása a virtuális gépnek
Az egyéni adatok használatához a base64-nek először kódolnia kell a tartalmat, mielőtt átadná azt az API-nak, kivéve, ha olyan CLI eszközt használ, amely az átalakítást végzi, például az AZ CLI. A méret nem haladhatja meg a 64 KB-ot.

A CLI-ben az egyéni adatokat fájlként adhatja át, és base64-re konvertálja.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Az Azure Resource Manager (ARM) függvényben van egy [base64 függvény.](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Egyéni adatok feldolgozása
A virtuális gépekre telepített létesítési ügynökök kezelik a platformmal való kapcsolódást, és a fájlrendszeren helyezik el. 

### <a name="windows"></a>Windows
Az egyéni adatok bináris fájlként kerülnek a *%SYSTEMDRIVE%\AzureData\CustomData.bin* fájlba, de nem kerülnek feldolgozásra. Ha fel szeretné dolgozni ezt a fájlt, egyéni lemezképet kell készítenie, és kódot kell írnia a CustomData.bin fájl feldolgozásához.

### <a name="linux"></a>Linux  
Linux operációs rendszer, egyéni adatok at a virtuális gép az ovf-env.xml fájlon keresztül, amely másolja a */var/lib/waagent* könyvtárba kiépítése során.  A Microsoft Azure Linux Agent újabb verziói is másolja a base64-kódolt adatokat *a /var/lib/waagent/CustomData* a kényelem érdekében.

Az Azure jelenleg két kiépítési ügynököt támogat:
* Linux-ügynök - Alapértelmezés szerint az ügynök nem dolgozza fel az egyéni adatokat, akkor létre kell készítenie egy egyéni lemezképet, amely engedélyezve van. A dokumentáció szerint [a](https://github.com/Azure/WALinuxAgent#configuration) vonatkozó beállítások a következők:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Ha engedélyezi az egyéni adatokat, és parancsfájlt hajt végre, késlelteti a virtuális gép jelentése, amely készen áll, vagy hogy a kiépítés sikeres volt, amíg a parancsfájl befejeződött. Ha a parancsfájl meghaladja a virtuális gép kiépítési időkeret40 mins, a virtuális gép létrehozása sikertelen lesz. Vegye figyelembe, ha a parancsfájl végrehajtása sikertelen, vagy a végrehajtás során hibák, nem minősül végzetes kiépítési hiba, létre kell hoznia egy értesítési útvonalat, hogy figyelmeztesse a parancsfájl befejezésének állapotát.

Az egyéni adatvégrehajtás hibaelhárításához tekintse át a */var/log/waagent.log című témakört.*

* cloud-init - Alapértelmezés szerint az egyéni adatokat dolgozza fel, a cloud-init [több egyéni adatformátumot](https://cloudinit.readthedocs.io/en/latest/topics/format.html) fogad el, például felhő-init konfigurációt, parancsfájlokat stb. Hasonló a Linux-ügynökhöz, amikor a cloud-init feldolgozza az egyéni adatokat. Ha a konfigurációs feldolgozás vagy parancsfájlok végrehajtása során hibák merülnek fel, nem minősül végzetes kiépítési hibának, és létre kell hoznia egy értesítési útvonalat, amely figyelmezteti a parancsfájl befejezési állapotára. Azonban a Linux-ügynöktől eltérő, a cloud-init nem várja meg a felhasználói egyéni adatkonfigurációk befejezéséhez, mielőtt a platformnak a virtuális gép készen áll. Az azure-alapú felhőalapú initekkel kapcsolatos további információkért tekintse meg a [dokumentációt.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)


Az egyéni adatvégrehajtás hibaelhárításához tekintse át a hibaelhárítási [dokumentációt.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)


## <a name="faq"></a>GYIK
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Frissíthetem az egyéni adatokat a virtuális gép létrehozása után?
Az egyes virtuális gépek esetében a virtuálisgép-modellben lévő egyéni adatok nem frissíthetők, de a VMSS esetében frissítheti a VMSS egyéni adatait REST API-n keresztül (ps vagy AZ CLI ügyfelekre nem alkalmazható). Amikor frissíti az egyéni adatokat a VMSS-modellben:
* A VMSS meglévő példányai nem kapják meg a frissített egyéni adatokat, csak addig, amíg újra nem kerülnek.
* A vMSS-ben frissített meglévő példányok nem kapják meg a frissített egyéni adatokat.
* Az új példányok megkapják az új egyéni adatokat.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Elhelyezhetek bizalmas értékeket az egyéni adatokban?
Azt tanácsoljuk, hogy **ne** tároljon bizalmas adatokat egyéni adatokban. További információ: [Azure Security and encryption gyakorlati tanácsok.](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)


### <a name="is-custom-data-made-available-in-imds"></a>Elérhetők az egyéni adatok az IMDS-ben?
Nem, ez a funkció jelenleg nem érhető el.
