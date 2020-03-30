---
title: Kívánt állapotkonfiguráció-bővítmény az Azure Resource Manager-sablonokkal
description: Ismerje meg a Resource Manager sablondefinícióját a kívánt állapotkonfigurációs (DSC) bővítményhez az Azure-ban.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: Dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: ef781653332984a7fb6d71ef91d53cbf77e6c91c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72437972"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Kívánt állapotkonfiguráció-bővítmény az Azure Resource Manager-sablonokkal

Ez a cikk a [kívánt állapotkonfigurációs (DSC) bővítménykezelő](dsc-overview.md)Azure Resource Manager-sablonját ismerteti. Számos példa a **RegistrationURL** (karakterláncként biztosított) és **RegistrationKey** [(pscredentialként](/dotnet/api/system.management.automation.pscredential)biztosított) az Azure Automation-szel való alaplaphoz. Az értékek beszerzéséről további információt [a Bevezetési gépek az Azure Automation State Configuration – Biztonságos regisztráció szolgáltatás általi kezelésért.](/azure/automation/automation-dsc-onboarding#secure-registration)

> [!NOTE]
> Előfordulhat, hogy némileg eltérő séma példákat. A séma változása a 2016 októberi kiadásban történt. További információt a [Frissítés korábbi formátumból .](#update-from-a-previous-format)

## <a name="template-example-for-a-windows-vm"></a>Példa sablonra Windows virtuális géphez

A következő kódrészlet a sablon **Erőforrás** szakaszába kerül.
A DSC-bővítmény örökli az alapértelmezett bővítménytulajdonságokat.
További információ: [VirtualMachineExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Sablon példa a Windows virtuálisgép-méretezési készleteihez

A virtuálisgép-méretezési csoport csomópontja rendelkezik egy **Tulajdonságszakasz,** amely rendelkezik egy **VirtualMachineProfile, extensionProfile** attribútummal.
A **bővítmények**csoportban adja meg a DSC-bővítmény részleteit.

A DSC-bővítmény örökli az alapértelmezett bővítménytulajdonságokat.
További információ: [VirtualMachineScaleSetExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

## <a name="detailed-settings-information"></a>Részletes beállításokkal kapcsolatos információk

Használja a következő sémát az Azure DSC-bővítmény **beállítások** szakaszában egy Resource Manager-sablonban.

Az alapértelmezett konfigurációs parancsfájlhoz elérhető argumentumok listáját az [Alapértelmezett konfigurációs parancsfájl című témakörben](#default-configuration-script)olvashatja.

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

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| beállítások.wmfVerzió |sztring |A Windows Management Framework (WMF) azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot **a legújabbra** állítja, a WMF legújabb verzióját telepíti. Jelenleg a tulajdonság egyetlen lehetséges értéke **a 4.0**, **5.0**, **5.1**és **a legújabb**. Ezek relevancia a frissítések hatálya alá tartoznak. Az alapértelmezett érték a **legújabb**. |
| settings.configuration.url |sztring |Itt adhatja meg azt az URL-címet, ahonnan a DSC konfigurációs .zip fájlját le szeretné tölteni. Ha a megadott URL-cím hez SAS-jogkivonat szükséges, állítsa be a **protectedSettings.configurationUrlSasToken** tulajdonságot a SAS-jogkivonat értékére. Erre a tulajdonságra akkor van szükség, ha **a settings.configuration.script** vagy **a settings.configuration.function** definiálva van. Ha ezekhez a tulajdonságokhoz nincs érték megadva, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt a Helykonfigurációkezelő (LCM) metaadatainak beállításához, és argumentumokat kell megadni. |
| settings.configuration.script |sztring |Megadja a DSC-konfiguráció definícióját tartalmazó parancsfájl fájlnevét. Ennek a parancsfájlnak a **settings.configuration.url** tulajdonság által megadott URL-címről letöltött .zip fájl gyökérmappájában kell lennie. Erre a tulajdonságra akkor van szükség, ha **a settings.configuration.url** vagy **a settings.configuration.script** definiálva van. Ha ezekhez a tulajdonságokhoz nincs érték megadva, a bővítmény meghívja az lcm metaadatok beállításához szükséges alapértelmezett konfigurációs parancsfájlt, és argumentumokat kell megadni. |
| settings.configuration.function |sztring |Megadja a DSC-konfiguráció nevét. A megnevezett konfigurációnak szerepelnie kell a **settings.configuration.script** által definiált parancsfájlban. Erre a tulajdonságra akkor van szükség, ha **a settings.configuration.url** vagy **a settings.configuration.function** definiálva van. Ha ezekhez a tulajdonságokhoz nincs érték megadva, a bővítmény meghívja az lcm metaadatok beállításához szükséges alapértelmezett konfigurációs parancsfájlt, és argumentumokat kell megadni. |
| settings.configurationArguments |Gyűjtemény |Meghatározza azokat a paramétereket, amelyeket át szeretne adni a DSC-konfigurációnak. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData.url |sztring |Itt adhatja meg azt az URL-címet, amelyről a konfigurációs adatokat (.psd1) a DSC-konfiguráció bemeneteként használni szeretné. Ha a megadott URL-cím hez SAS-jogkivonat szükséges, állítsa be a **protectedSettings.configurationDataSasToken** tulajdonságot a SAS-jogkivonat értékére. |
| settings.privacy.dataGyűjtemény |sztring |Engedélyezi vagy letiltja a telemetriai adatok gyűjteményét. A tulajdonság egyetlen lehetséges értéke: **Engedélyezés**, **Letiltás**, **''** vagy **$null.** Ha üresen hagyja ezt a tulajdonságot, vagy null értékre engedélyezi a telemetriai adatokat. Az alapértelmezett érték a **''**. További információ: [Azure DSC-bővítmény adatgyűjtés.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Gyűjtemény |Alternatív helyeket határoz meg, ahonnan a WMF-et le szeretné tölteni. További információ: [Azure DSC extension 2.8 and how to maps downloads of the extension dependencies to your own location.](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Gyűjtemény |Meghatározza azokat a paramétereket, amelyeket át szeretne adni a DSC-konfigurációnak. Ez a tulajdonság titkosított. |
| protectedSettings.configurationUrlSasToken |sztring |Megadja a **settings.configuration.url** által definiált URL-cím eléréséhez használandó SAS-jogkivonatot. Ez a tulajdonság titkosított. |
| protectedSettings.configurationDataUrlSasToken |sztring |Megadja a **settings.configurationData.url** által definiált URL-cím eléréséhez használandó SAS-jogkivonatot. Ez a tulajdonság titkosított. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az alábbi értékekről a [Helyi konfigurációkezelő alapbeállításai](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings)című témakörben olvashat bővebben.
A DSC-bővítmény alapértelmezett konfigurációs parancsfájljával csak az alábbi táblázatban felsorolt LCM-tulajdonságokat konfigurálhatja.

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Kötelező tulajdonság. Megadja azt a kulcsot, amelyet egy csomópont az Azure Automation-szolgáltatással egy PowerShell-hitelesítő objektum jelszavaként regisztrál. Ez az érték automatikusan felderíthető a **listkeys** metódus sal az Automation-fiókkal.  Lásd a [példát](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |sztring |Kötelező tulajdonság. Megadja az Automation-végpont URL-címét, ahol a csomópont regisztrálni próbál. Ez az érték automatikusan felderíthető a **referenciamódszer** használatával az Automation-fiókkal szemben. |
| settings.configurationArguments.NodeConfigurationName |sztring |Kötelező tulajdonság. Megadja a csomópont konfigurációját az Automation-fiókban, amelyet a csomóponthoz rendel. |
| settings.configurationArguments.ConfigurationMode |sztring |Megadja az LCM módját. Az érvényes beállítások a következők: **ApplyOnly**, **ApplyandMonitor**és **ApplyandAutoCorrect**.  Az alapértelmezett érték **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Itt adható meg, hogy az LCM milyen gyakran kíséreljen meg frissítéseket keresni az Automation-fiókkal.  Az alapértelmezett érték **30**.  A minimális érték **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Itt adható meg, hogy az LCM milyen gyakran érvényesítse az aktuális konfigurációt. Az alapértelmezett érték **15**. A minimális érték **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | logikai | Itt adható meg, hogy egy csomópont automatikusan újraindítható-e, ha egy DSC-művelet kéri azt. Az alapértelmezett érték **hamis.** |
| settings.configurationArguments.ActionAfterReboot | sztring | Itt adható meg, hogy mi történjen az újraindítás után a konfiguráció alkalmazásakor. Az érvényes beállítások a **ContinueConfiguration** és **a StopConfiguration**. Az alapértelmezett érték **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | logikai | Itt adható meg, hogy az LCM felülírja-e a csomóponton lévő meglévő modulokat. Az alapértelmezett érték **hamis.** |

## <a name="settings-vs-protectedsettings"></a>beállítások vs. protectedSettings

Minden beállítás a virtuális gép egy beállítási szövegfájljába kerül.
A **beállítások** alatt felsorolt tulajdonságok nyilvános tulajdonságok.
A nyilvános tulajdonságok nincsenek titkosítva a beállítások szövegfájljában.
A **protectedSettings** alatt felsorolt tulajdonságok egy tanúsítvánnyal vannak titkosítva, és nem jelennek meg egyszerű szövegben a virtuális gép beállításfájljában.

Ha a konfigurációnak hitelesítő adatokra van szüksége, a hitelesítő adatokat a **protectedSettings**-ba is felveheti:

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

A következő példa a DSC-bővítmény alapértelmezett viselkedését mutatja be, amely metaadat-beállításokat biztosít az LCM számára, és regisztráljon az Automation DSC szolgáltatásban.
Konfigurációs argumentumok szükségesek.
A konfigurációs argumentumok átkerülnek az lcm metaadatok beállításához az alapértelmezett konfigurációs parancsfájlba.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Példa a konfigurációs parancsfájl használatára az Azure Storage-ban

A következő példa a [DSC bővítménykezelő áttekintése](dsc-overview.md).
Ez a példa a bővítmény üzembe helyezéséhez parancsmagok helyett erőforrás-kezelő sablonokat használ.
Mentse az IisInstall.ps1 konfigurációt, helyezze el egy `iisinstall.zip`.zip fájlba (például: ), majd töltse fel a fájlt egy hozzáférhető URL-ben.
Ez a példa az Azure Blob storage-t használja, de .zip fájlokat tetszőleges helyről tölthet le.

Az Erőforrás-kezelő sablonban a következő kód arra utasítja a virtuális gép, hogy töltse le a megfelelő fájlt, majd futtassa a megfelelő PowerShell-függvényt:

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

## <a name="example-using-referenced-azure-automation-registration-values"></a>Példa hivatkozott Azure Automation regisztrációs értékek használatára

A következő példa lekéri a **RegistrationUrl** és **RegistrationKey** hivatkozva az Azure Automation-fiók tulajdonságai, és a **listkeys** metódus használatával az elsődleges kulcs (0) lekérésével.  Ebben a példában az **automationAccountName** és a **NodeConfigName** paramétereket biztosították a sablonhoz.

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

## <a name="update-from-a-previous-format"></a>Frissítés előző formátumból

A bővítmény korábbi formátumában megadott beállítások (és amelyek nyilvános tulajdonságokkal rendelkeznek **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**vagy **Properties)** automatikusan alkalmazkodnak a bővítmény aktuális formátumához.
Úgy futnak, mint korábban.

A következő séma bemutatja, hogyan nézett ki az előző beállításséma:

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

Az előző formátum a következőképpen alkalmazkodik az aktuális formátumhoz:

| Aktuális tulajdonság neve | Előző sémamegfelelője |
| --- | --- |
| beállítások.wmfVerzió |Beállítások. WMFVerzió |
| settings.configuration.url |Beállítások. ModulesUrl |
| settings.configuration.script |A beállítások első része. ConfigurationFunction (előtte \\ \\) |
| settings.configuration.function |A beállítások második része. ConfigurationFunction (utána \\ \\) |
| settings.configuration.module.name | Beállítások. Modulforrás |
| settings.configuration.module.version | Beállítások. ModuleVersion |
| settings.configurationArguments |Beállítások. Tulajdonságok |
| settings.configurationData.url |protectedSettings.DataBlobUri (SAS-token nélkül) |
| settings.privacy.dataGyűjtemény |Beállítások. Privacy.dataGyűjtemény |
| settings.advancedOptions.downloadMappings |Beállítások. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties (védettbeállítások). |
| protectedSettings.configurationUrlSasToken |Beállítások. SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-jogkivonat a protectedSettings.DataBlobUri webhelyről |

## <a name="troubleshooting"></a>Hibaelhárítás

Íme néhány hiba, amelybe belefuthat, és hogyan javíthatja ki őket.

### <a name="invalid-values"></a>Érvénytelen értékek

"Privacy.dataCollection is{0}' '.
Az egyetlen lehetséges érték a '', "Enable" és a 'Disable'.' (Csak a '', "Enable" és a 'Disable'.' (Csak a '', az 'Enable' és a 'Disable'.
"WmfVersion a{0}" '.
Csak a lehetséges értékek ... és a "legújabb".

**Probléma**: A megadott érték nem engedélyezett.

**Megoldás**: Módosítsa az érvénytelen értéket érvényes értékre.
További információt a Részletek című táblázatban [talál.](#details)

### <a name="invalid-url"></a>Érvénytelen URL-cím

"ConfigurationData.url a{0}' ' . Ez nem érvényes URL" "DataBlobUri{0}is ' '. Ez nem érvényes URL" "Configuration.url{0}is ' '. Ez nem érvényes URL"

**Probléma**: A megadott URL érvénytelen.

**Megoldás:** Ellenőrizze az összes megadott URL-t.
Győződjön meg arról, hogy az összes URL-cím érvényes helyekre oldódik, amelyeket a bővítmény a távoli számítógépen elérhet.

### <a name="invalid-registrationkey-type"></a>Érvénytelen RegistrationKey típus

"Érvénytelen típus a PSCredential típusú RegistrationKey paraméterhez."

**Probléma**: A protectedSettings.configurationArguments *regisztrációskulcs* értéke nem adható meg más típusúként, mint pscredential.

**Megoldás**: Módosítsa a registrationkey védettBeállítások.configurationArguments bejegyzését PSCredential típusúra a következő formátumban:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument típus

"Érvénytelen konfigurációArgumentumok {0}típusa "

**Probléma**: A *ConfigurationArguments* tulajdonság nem oldható fel **kivonatoló** táblaobjektummá.

**Megoldás:** A *ConfigurationArguments* tulajdonságot **kivonatoló táblává teheti.**
Kövesse az előző példákban megadott formátumot. Figyelje az idézeteket, vesszőket és fogszabályzókat.

### <a name="duplicate-configurationarguments"></a>Duplikált konfigurációs argumentumok

"Ismétlődő argumentumokat{0}találok ' nyilvános és védett konfigurációbanArgumentumok

**Probléma**: A *konfigurációs argumentumok* a nyilvános beállításokban és a *ConfigurationArguments* a védett beállítások tulajdonságai azonos nevű.

**Megoldás**: Távolítsa el az egyik ismétlődő tulajdonságot.

### <a name="missing-properties"></a>Hiányzó tulajdonságok

"beállításokat. Configuration.function megköveteli, hogy settings.configuration.url vagy settings.configuration.module meg van adva"

"beállításokat. Configuration.url megköveteli, hogy settings.configuration.script meg van adva"

"beállításokat. A Configuration.script használatához meg kell adni a settings.configuration.url-t"

"beállításokat. Configuration.url megköveteli, hogy settings.configuration.function meg van adva"

"protectedSettings.ConfigurationUrlSasToken megköveteli, hogy settings.configuration.url meg van adva"

"protectedSettings.ConfigurationDataUrlSasToken megköveteli, hogy settings.configurationData.url meg van adva"

**Probléma**: Egy definiált tulajdonságnak egy másik tulajdonságra van szüksége, amely hiányzik.

**Megoldások**:

- Adja meg a hiányzó tulajdonságot.
- Távolítsa el a hiányzó tulajdonságot tartalmazó tulajdonságot.

## <a name="next-steps"></a>További lépések

- Ismerje meg [a virtuálisgép-méretezési készletek használatát az Azure DSC-bővítményhasználatával.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)
- További részletek a [DSC biztonságos hitelesítő adatok kezeléséről.](dsc-credentials.md)
- Bevezetés [az Azure DSC bővítménykezelőbe.](dsc-overview.md)
- A PowerShell DSC-ről további információt a [PowerShell dokumentációs központjában](/powershell/scripting/dsc/overview/overview)talál.
