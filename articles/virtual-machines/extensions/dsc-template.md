---
title: A kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal
description: Ismerje meg a Resource Manager-sablon definícióját az Azure kívánt State Configuration (DSC) bővítményéhez.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 458ba61adba294af99f2265e4907e874ed3a6956
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084579"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>A kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal

Ez a cikk a [kívánt állapot-konfigurációs (DSC) bővítmény kezelője](dsc-overview.md)Azure Resource Manager sablonját ismerteti. Számos példa a **RegistrationURL** (karakterláncként megadott) és a **RegistrationKey** (feltéve, hogy [PSCredential](/dotnet/api/system.management.automation.pscredential)) használja a Azure Automationba való bevezetéshez. Az értékek beszerzésével kapcsolatos részletekért lásd: bevezetési [gépek felügyelethez Azure Automation állapot konfigurálása – biztonságos regisztráció](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Előfordulhat, hogy némileg eltérő séma-példákkal találkozhat. A séma változása a 2016 októberi kiadásban történt. Részletekért lásd: [korábbi formátum frissítése](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Sablon – példa Windows rendszerű virtuális géphez

A következő kódrészlet a sablon **erőforrás** szakaszában található.
A DSC-bővítmény örökli az alapértelmezett bővítmény tulajdonságait.
További információ: [VirtualMachineExtension osztály](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Példa sablonra a Windows rendszerű virtuálisgép-méretezési csoportokhoz

A virtuálisgép-méretezési csoport csomópontja tartalmaz egy **VirtualMachineProfile, extensionProfile** attribútummal rendelkező **Properties** szakaszt.
A **bővítmények**területen adja meg a DSC-bővítmény részleteit.

A DSC-bővítmény örökli az alapértelmezett bővítmény tulajdonságait.
További információ: [VirtualMachineScaleSetExtension osztály](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Részletes beállítások információi

A Resource Manager-sablonok Azure DSC bővítményének **Beállítások** szakaszában használja az alábbi sémát.

Az alapértelmezett konfigurációs parancsfájlhoz elérhető argumentumok listáját itt tekintheti meg: [alapértelmezett konfigurációs parancsfájl](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Részletek

| Tulajdonság neve | Type | Leírás |
| --- | --- | --- |
| settings.wmfVersion |Karakterlánc |A Windows Management Framework (WMF) azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot a **legújabbra** állítja, a a WMF legújabb verzióját telepíti. Jelenleg a tulajdonság egyetlen lehetséges értéke **4,0**, **5,0**, **5,1**és **Latest**. Ezek a lehetséges értékek a frissítések tárgya. Az alapértelmezett érték a **legújabb**. |
| settings.configuration.url |Karakterlánc |Azt az URL-címet adja meg, amelyből le szeretné tölteni a DSC Configuration. zip fájlt. Ha a megadott URL-címnek szüksége van egy SAS-tokenre a hozzáféréshez, állítsa a **protectedsettingsfromkeyvault. configurationUrlSasToken** tulajdonságot az SAS-token értékére. Ezt a tulajdonságot kötelező megadni, ha a Settings. **Configuration. script** vagy **Settings. Configuration. Function** definiálva van. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt a Location Configuration Manager (LCD) metaadatainak beállításához, és meg kell adni az argumentumokat. |
| settings.configuration.script |Karakterlánc |A DSC-konfiguráció definícióját tartalmazó parancsfájl fájlnevét adja meg. Ennek a parancsfájlnak a. zip fájl gyökérkönyvtárában kell lennie, amely a **Settings. Configuration. URL** tulajdonság által megadott URL-címről van letöltve. Ezt a tulajdonságot kötelező megadni, ha a Settings. **Configuration. URL** vagy **Settings. Configuration. script** definiálva van. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt, amely az LCD-metaadatokat állítja be, és az argumentumokat meg kell adni. |
| settings.configuration.function |Karakterlánc |Megadja a DSC-konfiguráció nevét. A nevű konfigurációnak szerepelnie kell a **Settings. Configuration. script** által definiált parancsfájlban. Ezt a tulajdonságot kötelező megadni, ha a Settings. **Configuration. URL** vagy **Settings. Configuration. Function** definiálva van. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt, amely az LCD-metaadatokat állítja be, és az argumentumokat meg kell adni. |
| settings.configurationArguments |Collection |Meghatározza a DSC-konfigurációnak átadni kívánt paramétereket. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData.url |Karakterlánc |Meghatározza azt az URL-címet, amelyből le szeretné tölteni a DSC-konfiguráció bemenetként használandó konfigurációs adatait (. psd1). Ha a megadott URL-címnek szüksége van egy SAS-tokenre a hozzáféréshez, állítsa a **protectedsettingsfromkeyvault. configurationDataUrlSasToken** tulajdonságot az SAS-token értékére. |
| Settings. privacy. Datacollection objektumot |Karakterlánc |Engedélyezheti vagy letilthatja a telemetria-gyűjteményt. A tulajdonság egyetlen lehetséges értéke a következő: **Engedélyezés**, **Letiltás**, **""** vagy **$Null**. Ha ezt a tulajdonságot üresen hagyja, vagy NULL értékűre teszi a telemetria. Az alapértelmezett érték: **""** . További információ: az [Azure DSC bővítmény adatgyűjtése](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Collection |Meghatározza azokat a másodlagos helyeket, amelyekről a WMF-t le szeretné tölteni. További információkért lásd: [Azure DSC-bővítmény 2,8 és hogyan lehet leképezni a bővítmény függőségeinek letöltését a saját helyére](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Collection |Meghatározza a DSC-konfigurációnak átadni kívánt paramétereket. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationUrlSasToken |Karakterlánc |Meghatározza a **Settings. Configuration. URL** által definiált URL-cím eléréséhez használandó sas-tokent. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationDataUrlSasToken |Karakterlánc |Meghatározza a **Settings. configurationData. URL** által definiált URL-cím eléréséhez használandó sas-tokent. Ez a tulajdonság titkosítva van. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

A következő értékekkel kapcsolatos további információkért lásd: [helyi Configuration Manager alapszintű beállítások](/powershell/dsc/metaconfig#basic-settings).
A DSC-bővítmény alapértelmezett konfigurációs parancsfájlja csak az alábbi táblázatban felsorolt LCD-tulajdonságok konfigurálására használható.

| Tulajdonság neve | Type | Leírás |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Kötelező tulajdonság. Meghatározza azt a kulcsot, amelyet egy csomóponthoz használ a Azure Automation szolgáltatásban egy PowerShell hitelesítőadat-objektum jelszavaként való regisztráláshoz. Ez az érték automatikusan felderíthető az Automation-fiók **listkeys műveletének beolvasása** metódusának használatával.  Lásd a [példát](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |Karakterlánc |Kötelező tulajdonság. Megadja annak az Automation-végpontnak az URL-címét, amelyben a csomópont megpróbál regisztrálni. Ez az érték automatikusan felderíthető az Automation-fiókra vonatkozó **hivatkozási** módszer használatával. |
| settings.configurationArguments.NodeConfigurationName |Karakterlánc |Kötelező tulajdonság. Megadja az Automation-fiók csomópont-konfigurációját a csomóponthoz való hozzárendeléshez. |
| settings.configurationArguments.ConfigurationMode |Karakterlánc |Megadja az LCD/ChipOnGlas üzemmódot. Az érvényes beállítások a következők: **ApplyOnly**, **ApplyandMonitor**és **ApplyandAutoCorrect**.  Az alapértelmezett érték a **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Meghatározza, hogy az LCD-eszköz milyen gyakran próbálkozzon az Automation-fiókkal a frissítésekhez.  Az alapértelmezett érték **30**.  A minimális érték **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Azt határozza meg, hogy az LCD/ChipOnGlas milyen gyakran ellenőrizze az aktuális konfigurációt. Az alapértelmezett érték **15**. A minimális érték **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Meghatározza, hogy a csomópontok automatikusan újraindulnak-e, ha egy DSC-művelet kéri. Az alapértelmezett érték **false (hamis**). |
| settings.configurationArguments.ActionAfterReboot | Karakterlánc | Itt adható meg, hogy mi történjen a konfiguráció alkalmazásának újraindításakor. Az érvényes beállítások a következők: **ContinueConfiguration** és **stopconfiguration metódusa**. Az alapértelmezett érték a **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Meghatározza, hogy a LCD-eszközök felülírják-e a csomóponton meglévő modulokat. Az alapértelmezett érték **false (hamis**). |

## <a name="settings-vs-protectedsettings"></a>beállítások és Protectedsettingsfromkeyvault

A rendszer minden beállítást a virtuális gép egy beállítási szövegfájlba ment.
A **Beállítások** területen felsorolt tulajdonságok nyilvános tulajdonságok.
A nyilvános tulajdonságok nem titkosítva vannak a beállítások szövegfájljában.
A **protectedsettingsfromkeyvault** alatt felsorolt tulajdonságok tanúsítvánnyal vannak titkosítva, és nem jelennek meg egyszerű szövegben a virtuális gép beállítási fájljában.

Ha a konfigurációhoz hitelesítő adatokra van szükség, a hitelesítő adatokat a **protectedsettingsfromkeyvault**-ben is megadhatja:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Példa konfigurációs parancsfájlra

Az alábbi példa a DSC-bővítmény alapértelmezett viselkedését mutatja be, amely az LCD-s és a Automation DSC szolgáltatásban való regisztráláshoz biztosít metaadat-beállításokat.
A konfigurációs argumentumok megadása kötelező.
A konfigurációs argumentumokat a rendszer az alapértelmezett konfigurációs parancsfájlnak adja át az LCD-metaadatok beállításához.

```json
"settings": {
    "RegistrationUrl" : "[parameters('registrationUrl1')]",
    "NodeConfigurationName" : "nodeConfigurationNameValue1"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Példa az Azure Storage konfigurációs parancsfájljának használatával

A következő példa a [DSC bővítmény kezelőjének áttekintése](dsc-overview.md).
Ez a példa Resource Manager-sablonokat használ a bővítmény telepítéséhez a parancsmagok helyett.
Mentse a IisInstall. ps1 konfigurációt, helyezze egy. zip fájlba (például: `iisinstall.zip`), majd töltse fel a fájlt egy elérhető URL-címen.
Ez a példa az Azure Blob Storage-t használja, de tetszőleges helyről letöltheti a. zip fájlokat.

A Resource Manager-sablonban a következő kód arra utasítja a virtuális gépet, hogy töltse le a megfelelő fájlt, majd futtassa a megfelelő PowerShell-függvényt:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Példa hivatkozott Azure Automation regisztrációs értékeket használó

A következő példa lekéri a **RegistrationUrl** és a **RegistrationKey** az Azure Automation fiók tulajdonságaira való hivatkozással, és a **listkeys műveletének beolvasása** metódus használatával kéri le az elsődleges kulcsot (0).  Ebben a példában a **automationAccountName** és a **NodeConfigName** paramétereket adtak meg a sablonhoz.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Frissítés korábbi formátumból

A bővítmény korábbi formátumában (és a nyilvános tulajdonságokkal rendelkező **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**vagy **Properties**) származó beállítások automatikusan a bővítmény aktuális formátumához alkalmazkodik.
Ugyanúgy futnak, mint korábban.

Az alábbi séma azt mutatja be, hogy az előző beállítások séma milyen módon nézett ki:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Az előző formátum az aktuális formátumhoz alkalmazkodik:

| Aktuális tulajdonságnév | Korábbi séma-egyenérték |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |A beállítások első része. ConfigurationFunction (előtte \\) \\ |
| settings.configuration.function |A beállítások második része. ConfigurationFunction (utána \\) \\ |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |Protectedsettingsfromkeyvault. DataBlobUri (SAS-token nélkül) |
| Settings. privacy. Datacollection objektumot |beállítások. Adatvédelem. Datacollection objektumot |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token a Protectedsettingsfromkeyvault. DataBlobUri |

## <a name="troubleshooting"></a>Hibaelhárítás

Íme néhány, a hibák, amelyek bejelentkezhetnek, és hogyan javíthatja azokat.

### <a name="invalid-values"></a>Érvénytelen értékek

"Az{0}adatvédelem. datacollection objektumot:" ".
Az egyetlen lehetséges érték: "", "Enable" és "Disable" ".
"A{0}WmfVersion:" ".
Csak a lehetséges értékek:... és a "legújabb" ".

**Probléma**: Egy megadott érték nem engedélyezett.

**Megoldás**: Módosítsa az érvénytelen értéket érvényes értékre.
További információ: táblázat, [részletek](#details).

### <a name="invalid-url"></a>Érvénytelen URL

"A ConfigurationData. URL a{0}következő:" ". Ez nem egy érvényes URL-cím: "" DataBlobUri{0}: "". Ez nem egy érvényes URL-cím: "" Configuration. URL{0}-cím: "". Ez nem egy érvényes URL-cím: "

**Probléma**: A megadott URL-cím érvénytelen.

**Megoldás**: Az összes megadott URL-cím bejelölése.
Győződjön meg arról, hogy az összes URL-cím a bővítmény által a távoli gépen elérhető érvényes helyekre van feloldva.

### <a name="invalid-registrationkey-type"></a>Érvénytelen RegistrationKey-típus

"A PSCredential típusú RegistrationKey paraméter típusa érvénytelen."

**Probléma**: A Protectedsettingsfromkeyvault. configurationArguments *RegistrationKey* értéke nem adható meg a PSCredential eltérő típusként.

**Megoldás**: Módosítsa a Protectedsettingsfromkeyvault. configurationArguments bejegyzést a RegistrationKey PSCredential-típusra a következő formátum használatával:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument-típus

"Érvénytelen configurationArguments típus {0}"

**Probléma**: A *ConfigurationArguments* tulajdonság nem oldható fel **kivonatoló tábla** objektumra.

**Megoldás**: Készítse el a *ConfigurationArguments* tulajdonságát.
Kövesse az előző példákban megadott formátumot. Tekintse meg az idézőjeleket, a vesszőket és a kapcsos zárójeleket.

### <a name="duplicate-configurationarguments"></a>Ismétlődő ConfigurationArguments

A "" duplikált{0}argumentumok találhatók a nyilvános és a védett configurationArguments is "

**Probléma**: A nyilvános beállításokban található *ConfigurationArguments* és a védett beállítások *ConfigurationArguments* azonos nevű tulajdonságokkal rendelkeznek.

**Megoldás**: Távolítsa el a duplikált tulajdonságok egyikét.

### <a name="missing-properties"></a>Hiányzó tulajdonságok

beállítások. A Configuration. Function működéséhez a Settings. Configuration. URL vagy a Settings. Configuration. Module megadása szükséges.

beállítások. A Configuration. URL-címnek szüksége van a Settings. Configuration. script paraméterre.

beállítások. A Configuration. script paraméterhez a Settings. Configuration. URL megadása szükséges.

beállítások. A Configuration. URL-címnek szüksége van a Settings. Configuration. Function megadott értékre.

"a Protectedsettingsfromkeyvault. ConfigurationUrlSasToken megköveteli, hogy a Settings. Configuration. URL-cím meg legyen adva"

"a Protectedsettingsfromkeyvault. ConfigurationDataUrlSasToken megköveteli, hogy a Settings. configurationData. URL meg legyen adva."

**Probléma**: Egy definiált tulajdonsághoz egy másik tulajdonságra van szükség, amely hiányzik.

**Megoldások**:

- Adja meg a hiányzó tulajdonságot.
- Távolítsa el a hiányzó tulajdonságot igénylő tulajdonságot.

## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan használhatja a virtuálisgép-méretezési csoportokat az Azure DSC bővítménnyel](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- További információ a [DSC biztonságos hitelesítőadat](dsc-credentials.md)-kezeléséről.
- Ismerkedjen [meg az Azure DSC bővítmény kezelőjével](dsc-overview.md).
- A PowerShell DSC-vel kapcsolatos további információkért nyissa meg a [PowerShell dokumentációs](/powershell/dsc/overview)központját.
