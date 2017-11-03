---
title: "Azure-sablonok között összetett értéket átadni |} Microsoft Docs"
description: "Azt mutatja be megközelítés használatos összetett objektumok állapotadatok megosztása Azure Resource Manager-sablonok és a kapcsolt sablonok használata ajánlott."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fd2f5e2d-d56d-4e01-a57d-34f3eaead4a9
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: tomfitz
ms.openlocfilehash: e163f3c2e9a78b057dc2a7a42924c59d0aac3fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="share-state-to-and-from-azure-resource-manager-templates"></a>Fájlmegosztási állapot és az Azure Resource Manager-sablonok
Ez a témakör gyakorlati tanácsok a kezelése és megosztása a sablonokon belüli állapotát jeleníti meg. A paraméterek és változók ebben a témakörben szereplő példák az objektumtípus adhat meg a telepítési követelményeket kényelmesen rendszerezéséhez. Ezekben a példákban a saját objektumok, amelyek a környezetben jelentéssel bírnak valósíthat meg.

Ez a témakör egy nagyobb tanulmány részét képezi. Olvassa el a teljes dokumentum, le kell töltenie [globális osztály Resource Manager sablonok szempontok és eljárások bizonyítása](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## <a name="provide-standard-configuration-settings"></a>Adja meg a szabványos konfigurációs beállítások
Helyett a sablont, amely a teljes és számos változata kínál, a közös minta arra, hogy a kijelölt ismert konfigurációk. Érvényben a felhasználók kiválaszthatják például védőfal kis, közepes és nagy szabványos pólóra méretét. Más pólóra méretű például a termék ajánlatokat, community edition vagy enterprise edition. Más esetekben lehet munkaterhelés-specifikus konfigurációk egy technológia – például a térkép csökkentése vagy a nem sql.

Összetett objektumok, amelyek tartalmazzák a gyűjtött adatokat, más néven "tulajdonságcsomagok" változók létrehozása, és az adatokat az erőforrás deklarációja alapjául a sablonban. Ezt a módszert biztosít az ügyfelek különböző méretű előre konfigurált megfelelő, ismert konfigurációk. Ismert konfigurációk nélkül felhasználók sablon kell önállóan méretezése fürtön határozza meg, figyelembe a platform erőforrás-korlátozások és számításokat végezni az eredményül kapott particionálás storage-fiókok és egyéb erőforrások (mert a fürt méretét és az erőforrás azonosításához megkötések). Azonkívül, hogy hatékonyabb működését az ügyfél, néhány ismert konfigurációk könnyebben is támogatja, és segít a következők támogatásával sűrűség magasabb szintű.

A következő példa bemutatja, hogyan adja meg a gyűjtött adatokat a lapblobok az összetett objektumokat tartalmazó változókat. A gyűjtemények megadása a virtuális gép méretét, a hálózati beállításokat, az operációs rendszeri beállítások és a rendelkezésre állási beállítások használt értékek.

```json
"variables": {
  "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  },
  "tshirtSizeMedium": {
    "vmSize": "Standard_A3",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
    "vmCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1],
      "jumpbox": 0
    }
  },
  "tshirtSizeLarge": {
    "vmSize": "Standard_A4",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
    "vmCount": 3,
    "slaveCount": 2,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 2,
      "pool": "db",
      "map": [0,1,1],
      "jumpbox": 0
    }
  },
  "osSettings": {
    "scripts": [
      "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
    ],
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer",
      "sku": "14.04.2-LTS",
      "version": "latest"
    }
  },
  "networkSettings": {
    "vnetName": "[parameters('virtualNetworkName')]",
    "addressPrefix": "10.0.0.0/16",
    "subnets": {
      "dmz": {
        "name": "dmz",
        "prefix": "10.0.0.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      },
      "data": {
        "name": "data",
        "prefix": "10.0.1.0/24",
        "vnet": "[parameters('virtualNetworkName')]"
      }
    }
  },
  "availabilitySetSettings": {
    "name": "pgsqlAvailabilitySet",
    "fdCount": 3,
    "udCount": 5
  }
}
```

Figyelje meg, hogy a **tshirtSize** változó összefűzi egy paraméteren keresztül megadott pólóra méretét (**kis**, **Közepes**, **nagy** ) a szöveg **tshirtSize**. Ez a változó segítségével pólóra méret társított összetett objektumot változója beolvasása.

Ezek a változók a sablonban később majd hivatkozhat. Nevű változókat és a Tulajdonságok lehetőséget egyszerűbbé teszi a sablonok szintaxisát, és megkönnyíti, hogy tudni, hogy a környezetben. A következő példa egy erőforrás értékeinek beállításához korábban megjelenített objektumok használatával történő telepítéséről határozza meg. A Virtuálisgép-méretet lekérésével értékét állítsa például `variables('tshirtSize').vmSize` során az értéket, a lemez méretét veszi át a `variables('tshirtSize').diskSize`. Ezenkívül csatolt sablon URI-JÁNAK értéke beállított `variables('tshirtSize').vmTemplate`.

```json
"name": "master-node",
"type": "Microsoft.Resources/deployments",
"apiVersion": "2015-01-01",
"dependsOn": [
    "[concat('Microsoft.Resources/deployments/', 'shared')]"
],
"properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[variables('tshirtSize').vmTemplate]",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "value": "[parameters('adminPassword')]"
      },
      "replicatorPassword": {
        "value": "[parameters('replicatorPassword')]"
      },
      "osSettings": {
        "value": "[variables('osSettings')]"
      },
      "subnet": {
        "value": "[variables('networkSettings').subnets.data]"
      },
      "commonSettings": {
        "value": {
          "region": "[parameters('region')]",
          "adminUsername": "[parameters('adminUsername')]",
          "namespace": "ms"
        }
      },
      "storageSettings": {
        "value":"[variables('tshirtSize').storage]"
      },
      "machineSettings": {
        "value": {
          "vmSize": "[variables('tshirtSize').vmSize]",
          "diskSize": "[variables('tshirtSize').diskSize]",
          "vmCount": 1,
          "availabilitySet": "[variables('availabilitySetSettings').name]"
        }
      },
      "masterIpAddress": {
        "value": "0"
      },
      "dbType": {
        "value": "MASTER"
      }
    }
  }
}
```

## <a name="pass-state-to-a-template"></a>A sablonok hozzáférési állapota
Megosztott állapot paramétereknek, hogy közvetlenül a telepítés során a sablonba.

A következő táblázat a sablonok a gyakran használt paraméterek.

| Név | Érték | Leírás |
| --- | --- | --- |
| location |Az Azure-régiók korlátozott listájából karakterlánc |A hely, ahol az erőforrások telepítése. |
| storageAccountNamePrefix |Karakterlánc |A Tárfiók, ahol a virtuális gép lemezeinek kerülnek egyedi DNS-neve |
| Tartománynév |Karakterlánc |A nyilvánosan elérhető jumpbox VM formátumú tartománynevet: **{tartománynév}. { hely}.cloudapp.com** például: **mydomainname.westus.cloudapp.azure.com** |
| adminUsername |Karakterlánc |A virtuális gépek felhasználónév |
| adminPassword |Karakterlánc |A virtuális gépek jelszava |
| tshirtSize |A korlátozott listájából karakterlánc kínált pólóra méretek |A nevesített skálázási egység méretét kiépítését. Például "Kicsi", "közepes", "Nagy" |
| virtualNetworkName |Karakterlánc |A fogyasztó kívánja használni a virtuális hálózat neve. |
| enableJumpbox |Korlátozott listájából (engedélyezett vagy letiltott) karakterlánc |A paraméter, amely azonosítja, hogy a környezet egy jumpbox engedélyezi-e. Értékek: "engedélyezve", "Letiltva" |

A **tshirtSize** az előző szakaszban használt paraméter típusúként van definiálva:

```json
"parameters": {
  "tshirtSize": {
    "type": "string",
    "defaultValue": "Small",
    "allowedValues": [
      "Small",
      "Medium",
      "Large"
    ],
    "metadata": {
      "Description": "T-shirt size of the MongoDB deployment"
    }
  }
}
```

## <a name="pass-state-to-linked-templates"></a>Állapot átadása kapcsolt sablonok
Csatolt sablonok való csatlakozáskor gyakran statikus vegyesen használ, és létrehozott változókat.

### <a name="static-variables"></a>Statikus változó
Statikus változó gyakran használják arra, hogy alapértékek, például a használt URL-, egy sablon egész.

A következő sablon cikkből a `templateBaseUrl` a sablon legfelső szintű helyét adja meg a Githubon. A következő sorban összeállít egy új változót `sharedTemplateUrl` , amely összefűzi a megosztott erőforrások sablon ismert nevű alap URL-címet. A sor alatt egy összetett objektum változó fogja tárolni Póló mérete, ha az alap URL-címet a ismert konfiguráció sablon helyére összefűzendő és tárolja a `vmTemplate` tulajdonság.

Ezt a módszert használja az az előnye, hogy a sablon helye megváltozik, ha csak módosítani szeretné az egyik helyen, amely továbbítja azokat a kapcsolt sablonok teljes statikus változó.

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "tshirtSizeSmall": {
    "vmSize": "Standard_A1",
    "diskSize": 1023,
    "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
    "vmCount": 2,
    "slaveCount": 1,
    "storage": {
      "name": "[parameters('storageAccountNamePrefix')]",
      "count": 1,
      "pool": "db",
      "map": [0,0],
      "jumpbox": 0
    }
  }
}
```

### <a name="generated-variables"></a>Létrehozott változók
Statikus változó mellett a több változók generálása dinamikusan történik. Ez a szakasz azonosítja az egyes létrehozott változók a gyakori hibatípusokat.

#### <a name="tshirtsize"></a>tshirtSize
A fenti példákban a létrehozott változó tisztában.

#### <a name="networksettings"></a>networkSettings
Kapacitás, funkció, vagy végpont hatókörön belüli megoldássablonban a kapcsolt sablonok általában létre meglévő erőforrásokat a hálózaton. Egy egyszerű megközelítése, hogy egy összetett objektumot használja a hálózati beállítások tárolásához adja meg azokat a kapcsolt sablonok.

Hálózati beállítások kommunikáció például alább látható.

```json
"networkSettings": {
  "vnetName": "[parameters('virtualNetworkName')]",
  "addressPrefix": "10.0.0.0/16",
  "subnets": {
    "dmz": {
      "name": "dmz",
      "prefix": "10.0.0.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    },
    "data": {
      "name": "data",
      "prefix": "10.0.1.0/24",
      "vnet": "[parameters('virtualNetworkName')]"
    }
  }
}
```

#### <a name="availabilitysettings"></a>availabilitySettings
A csatolt sablonok létrejött erőforrásokat gyakran kerülnek egy rendelkezésre állási csoportot. A következő példában a rendelkezésre állási készlet neve meg van adva, de is a tartalék tartomány és a frissítési tartományok száma használatára.

```json
"availabilitySetSettings": {
  "name": "pgsqlAvailabilitySet",
  "fdCount": 3,
  "udCount": 5
}
```

Ha több rendelkezésre állási csoportok (például egy fő csomópontok) és egy másik adatok csomópontok előtagjaként is használhatja a nevét, adjon meg több rendelkezésre állási csoportok, vagy a modell létrehozásához egy adott pólóra méretű változót korábban bemutatott kövesse.

#### <a name="storagesettings"></a>storageSettings
Csatolt sablonok gyakran megosztott tárolási részleteit. Az alábbi példában egy *storageSettings* objektum tartalmazza a tárolási fiók és a tároló neve adatait.

```json
"storageSettings": {
    "vhdStorageAccountName": "[parameters('storageAccountName')]",
    "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
    "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
}
```

#### <a name="ossettings"></a>osSettings
Csatolt sablonok szükség lehet operációs rendszer beállításait átadása különféle csomópontok különböző ismert konfigurációs típusok között. Egy összetett objektum tárolására és megosztására operációsrendszer-információkat egyszerűen és is megkönnyíti több operációs rendszer választást is támogatnak a központi telepítéshez.

A következő példa bemutatja egy objektumot *osSettings*:

```json
"osSettings": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "14.04.2-LTS",
    "version": "latest"
  }
}
```

#### <a name="machinesettings"></a>machineSettings
A létrehozott változó *machineSettings* core változók a virtuális gép létrehozása vegyesen tartalmazó összetett objektum. A változók közé tartoznak a rendszergazdai felhasználónevet és jelszót, egy virtuális gép nevét, és az operációs rendszer Képhivatkozás előtagot.

```json
"machineSettings": {
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "machineNamePrefix": "mongodb-",
    "osImageReference": {
        "publisher": "[variables('osFamilySpec').imagePublisher]",
        "offer": "[variables('osFamilySpec').imageOffer]",
        "sku": "[variables('osFamilySpec').imageSKU]",
        "version": "latest"
    }
},
```

Vegye figyelembe, hogy *osImageReference* értékeinek lekéri a *osSettings* a fő sablonban definiált változó. Ez azt jelenti, hogy a virtuális gépek könnyen módosíthatja az operációs rendszer – teljes egészében vagy egy sablon fogyasztó beállítás alapján.

#### <a name="vmscripts"></a>vmScripts
A *vmScripts* objektum részletesen ismerteti a parancsfájlok letöltésére és végrehajtására a Virtuálisgép-példány, beleértve a külső és belső hivatkozik. Kívül hivatkozásokat tartalmaznak az infrastruktúra.
Belső hivatkozásokat tartalmaznak, a telepített szoftvereket és a konfigurációs.

Használja a *scriptsToDownload* tulajdonság a parancsfájlok töltse le a virtuális gép listázásához. Ez az objektum parancssori argumentumokat használni a különböző típusú műveleteket mutató hivatkozásokat is tartalmaz. Ilyen műveletek közé tartoznak az alapértelmezett telepítés minden egyes csomóponton, egy telepítés, amelyen az összes csomópont telepítése után és lehet, hogy a megadott sablonkonfigurációs vonatkozó további parancsfájlok végrehajtása.

Ebben a példában a MongoDB, amelyhez szükséges egy soron képes biztosítani a magas rendelkezésre állású telepítésére használt sablon származik. A *arbiterNodeInstallCommand* hozzá lett adva *vmScripts* a soron telepítéséhez.

A változók szakaszban, ahol megtalálja a változókat, amelyek meghatározzák az adott szöveget, futtassa a parancsfájlt a megfelelő értékekkel.

```json
"vmScripts": {
    "scriptsToDownload": [
        "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
        "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],
    "regularNodeInstallCommand": "[variables('installCommand')]",
    "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
    "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
},
```

## <a name="return-state-from-a-template"></a>Egy sablonból visszatérési állapota
Nem csak is át adatokat sablonba, megoszthatja a hívó sablon vissza az adatokat. Az a **kimenete** szakasz csatolt sablon, megadhatja a kulcs/érték párok, amelyeket a Forrássablon lehet használni.

A következő példa bemutatja, hogyan felelt meg a magánhálózati IP-cím, egy csatolt sablon hozott létre.

```json
"outputs": {
    "masterip": {
        "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
        "type": "string"
     }
}
```

A fő sablonon belül az adatokat a következő szintaxissal:

    "[reference('master-node').outputs.masterip.value]"

A kifejezés a kimenetek szakasz vagy az erőforrások terület a fő sablon használható. A kifejezés nem használható a változók szakaszban, mert a futásidejű állapot támaszkodnak. Ez az érték a fő sablonból visszaállításához használja:

```json
"outputs": {
  "masterIpAddress": {
    "value": "[reference('master-node').outputs.masterip.value]",
    "type": "string"
  }
```

A csatolt sablon kimenetek szakaszának segítségével a virtuális gép adatlemezek vissza egy példát, lásd: [hozzon létre egy virtuális gép több adatlemezek](resource-group-create-multiple.md).

## <a name="define-authentication-settings-for-virtual-machine"></a>Virtuális gép hitelesítési beállításainak megadása
A konfigurációs beállításokat a korábban bemutatott minta segítségével adja meg a hitelesítési beállításokat a virtuális gép. Egy sikeres paramétert hoz létre a hitelesítés típusát.

```json
"parameters": {
  "authenticationType": {
    "allowedValues": [
      "password",
      "sshPublicKey"
    ],
    "defaultValue": "password",
    "metadata": {
      "description": "Authentication type"
    },
    "type": "string"
  }
}
```

A különböző hitelesítési típusok változók hozzáadta, és ez a paraméter értéke alapján központi telepítéshez használt egy változó tárolásához típusát.

```json
"variables": {
  "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
  "osProfilepassword": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]"
  },
  "osProfilesshPublicKey": {
    "adminUsername": "notused",
    "computerName": "[parameters('vmName')]",
    "customData": "[base64(variables('customData'))]",
    "linuxConfiguration": {
      "disablePasswordAuthentication": "true",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "[parameters('sshPublicKey')]",
            "path": "/home/notused/.ssh/authorized_keys"
          }
        ]
      }
    }
  }
}
```

A virtuális gép meghatározásakor beállíthatja a **osProfile** létrehozott változóhoz.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  ...
  "osProfile": "[variables('osProfile')]"
}
```

## <a name="next-steps"></a>Következő lépések
* A sablon szakaszai további tudnivalókért lásd: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md)
* A sablonon belül elérhető funkciókat, olvassa el [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md)
