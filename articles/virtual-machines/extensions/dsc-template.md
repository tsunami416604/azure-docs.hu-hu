---
title: Az Azure Resource Manager-sablonok Desired State Configuration bővítmény
description: További információ a Desired State Configuration (DSC) bővítmény, az Azure Resource Manager sablon definíciója.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 1dcbc8e0221689a6ece7e061d4b1a2632986ae84
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224374"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok Desired State Configuration bővítmény

Ez a cikk ismerteti az Azure Resource Manager-sablon esetében a [Desired State Configuration (DSC) bővítmény kezelő](dsc-overview.md).

> [!NOTE]
> Némileg eltérő séma példák léphetnek fel. A séma megváltozott a 2016. október kiadásban. További információkért lásd: [frissítés egy előző formátumból](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Windows Virtuálisgép-sablon példa

Az alábbi kódrészlet kerül a **erőforrás** szakaszában a sablont.
A DSC-bővítmény alapértelmezett bővítménytulajdonságok örökli.
További információkért lásd: [VirtualMachineExtension osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl"
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>A Windows virtuálisgép-méretezési csoport sablon példa

Egy virtuális gép méretezési készlet csomópont tartalmaz egy **tulajdonságok** szakaszt, amely rendelkezik egy **VirtualMachineProfile, extensionProfile** attribútum.
A **bővítmények**, adhatja hozzá a DSC-bővítmény adatait.

A DSC-bővítmény alapértelmezett bővítménytulajdonságok örökli.
További információkért lásd: [VirtualMachineScaleSetExtension osztály](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl"
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>Részletes beállítási információk

Használja a következő sémának a **beállítások** szakasz a Resource Manager-sablon Azure DSC-bővítmény.

Érhetők el az alapértelmezett konfigurációs szkript a következő paraméterek listáját lásd: [alapértelmezett konfigurációs parancsfájl](#default-configuration-script).

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
| settings.wmfVersion |sztring |Adja meg a verziót a Windows Management Framework (WMF), amely a virtuális Gépen kell telepíteni. Ez a tulajdonság **legújabb** a WMF legújabb verzióját telepíti. Ez a tulajdonság csak lehetséges értékei jelenleg **4.0**, **5.0**, **5.0PP**, és **legújabb**. Ezek lehetséges értékek a következők vonatkoznak a frissítéseket. Az alapértelmezett érték **legújabb**. |
| settings.configuration.url |sztring |Itt adhatja meg, ahonnan letölthető a DSC-konfiguráció .zip fájl URL-címét. Ha a megadott URL-cím egy SAS-tokent igényel hozzáférést, állítsa be a **protectedSettings.configurationUrlSasToken** tulajdonság értéke az SAS-jogkivonatot. Ez a tulajdonság nem kötelező, ha **settings.configuration.script** vagy **settings.configuration.function** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl helye Configuration Manager (LCM) Konfigurálása metaadatok beállítása és argumentumot kell megadni. |
| settings.configuration.script |sztring |Megadja a fájl nevét, a parancsfájl, amely tartalmazza a DSC-konfiguráció definíciójának. Ez a parancsfájl a megadott URL-címről letöltött .zip fájl a gyökérmappában kell lennie a **configuration.url** tulajdonság. Ez a tulajdonság nem kötelező, ha **settings.configuration.url** vagy **settings.configuration.script** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása és argumentumot kell megadni. |
| settings.configuration.function |sztring |Megadja a DSC-konfiguráció nevét. A konfiguráció nevű szerepelnie kell a parancsfájlt, amely **configuration.script** határozza meg. Ez a tulajdonság nem kötelező, ha **settings.configuration.url** vagy **settings.configuration.function** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása és argumentumot kell megadni. |
| settings.configurationArguments |Gyűjtemény |Határozza meg, amely a DSC-konfiguráció átadni kívánt paramétereket. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData.url |sztring |Adja meg az URL-cím, ahonnan letölthető a konfigurációs adatfájl (.psd1) való használatra a DSC-konfiguráció bemenetként. Ha a megadott URL-cím egy SAS-tokent igényel hozzáférést, állítsa be a **protectedSettings.configurationDataUrlSasToken** tulajdonság értéke az SAS-jogkivonatot. |
| settings.privacy.dataEnabled |sztring |Engedélyezheti vagy letilthatja a telemetriai adatok gyűjtése. Ez a tulajdonság az egyetlen lehetséges értékei a következők **engedélyezése**, **letiltása**, **''**, vagy **$null**. És ez a tulajdonság üres vagy null értékű lehetővé teszi, hogy a telemetria. Az alapértelmezett érték **''**. További információkért lásd: [Azure DSC bővítmény adatgyűjtés](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Gyűjtemény |Határozza meg a másodlagos helyeket, ahonnan letölthető a WMF. További információkért lásd: [Azure DSC bővítmény 2.8-as és a bővítmény függőségi letöltések leképezése a saját helyét](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Gyűjtemény |Határozza meg, amely a DSC-konfiguráció átadni kívánt paramétereket. Ez a tulajdonság titkosított. |
| protectedSettings.configurationUrlSasToken |sztring |Adja meg a SAS-jogkivonat használatával a URL-címet, amely **configuration.url** határozza meg. Ez a tulajdonság titkosított. |
| protectedSettings.configurationDataUrlSasToken |sztring |Adja meg a SAS-jogkivonat használatával a URL-címet, amely **configurationData.url** határozza meg. Ez a tulajdonság titkosított. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

A következő értékeket kapcsolatos további információkért lásd: [helyi Configuration Manager alapbeállítások](/powershell/dsc/metaconfig#basic-settings).
A DSC-bővítmény alapértelmezett konfigurációs parancsfájl segítségével az alábbi táblázat csak a felsorolt LCM tulajdonságok konfigurálása.

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Kötelező tulajdonság. Megadja a kulcsot, amellyel egy csomópont regisztrálása az Azure Automation szolgáltatással a PowerShell hitelesítő objektumot jelszóként. Ezt az értéket automatikusan felderített használatával a **listkeys műveletének** metódus az Automation-fiók ellen. Az érték egy védett beállításként védelméhez. |
| settings.configurationArguments.RegistrationUrl |sztring |Kötelező tulajdonság. Adja meg a végpont URL-címét az Automation, a csomópont próbál meg regisztrálni. Ezt az értéket automatikusan felderített használatával a **referencia** metódus az Automation-fiók ellen. |
| settings.configurationArguments.NodeConfigurationName |sztring |Kötelező tulajdonság. Adja meg a csomópont-konfiguráció hozzárendelése a csomópontot az Automation-fiókot. |
| settings.configurationArguments.ConfigurationMode |sztring |Az LCM meghatározza a módját. Érvényes lehetőségek a következők **ApplyOnly**, **ApplyandMonitor**, és **ApplyandAutoCorrect**.  Az alapértelmezett érték **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | UInt32 | Megadja, hogy milyen gyakran LCM ellenőrizze a frissítéseket az Automation-fiókot.  Alapértelmezett érték **30**.  Minimális érték **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Itt adhatja meg, milyen gyakran az LCM ellenőrzi az aktuális konfigurációt. Alapértelmezett érték **15**. Minimális érték **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | logikai | Itt adhatja meg, e csomópont is automatikusan újraindul, ha egy DSC művelet azt kéri. Alapértelmezett érték **hamis**. |
| settings.configurationArguments.ActionAfterReboot | sztring | Itt adhatja meg, mi történik, a rendszer újraindítása után a konfiguráció alkalmazásakor. Az érvényes beállítások: **ContinueConfiguration** és **StopConfiguration**. Alapértelmezett érték **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | logikai | Itt adhatja meg, hogy LCM felülírja a meglévő modulok a csomóponton. Alapértelmezett érték **hamis**. |

## <a name="settings-vs-protectedsettings"></a>Beállítások vs. ProtectedSettings

Az összes beállítások mentése a virtuális gép beállításai a fájlt.
Tartozó Tulajdonságok **beállítások** nyilvános tulajdonságok vannak.
A beállítások szövegfájl nyilvános tulajdonságok nincsenek titkosítva.
Tartozó Tulajdonságok **protectedSettings** van titkosítva, egy tanúsítványt, és nem jelennek meg a beállítások-fájlt a virtuális gép egyszerű szöveget.

Ha a konfigurációs van szüksége a hitelesítő adatokat, megadhatja a hitelesítő adatok **protectedSettings**:

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

## <a name="example-configuration-script"></a>Példa konfigurációs parancsfájl

Az alábbi példa bemutatja az alapértelmezett viselkedést, a DSC-bővítmény, amely LCM metaadat-beállításainak nyújtson, és regisztrálnia kell az Automation DSC szolgáltatással.
Konfigurációs argumentumokra szükség.
Konfigurációs argumentumok a LCM metaadatainak alapértelmezett konfigurációs parancsfájlt.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1')]"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>A példában a konfigurációs parancsfájl használata az Azure Storage-ban

A következő példa a [DSC bővítmény kezelő áttekintése](dsc-overview.md).
Ebben a példában helyett parancsmagok Resource Manager-sablonok használatával a bővítmény telepítése.
A IisInstall.ps1 konfiguráció mentéséhez, helyezze egy .zip fájlt, és majd feltöltjük a fájlt az elérhető URL-címre.
Ebben a példában az Azure Blob-tárolót használ, de .zip fájlokat tölthet le bármely tetszőleges helyre.

A Resource Manager-sablonban a következő kód arra utasítja a virtuális Gépre, és töltse le a megfelelő fájlt, és futtassa a megfelelő PowerShell-függvény:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>Egy előző formátumból frissítése

A bővítmény előző formátumú beállítások (és amelyek a nyilvános tulajdonságok **ModulesUrl**, **ConfigurationFunction**, **SasToken**, vagy  **Tulajdonságok**) automatikusan alkalmazkodik a bővítmény formátumban.
A Futtatás mint előtt.

A következő séma milyen az előző beállítások séma hasonlított mutatja be:

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

Itt látható, hogyan a korábbi formátumot alkalmazkodik a formátumban:

| Tulajdonság neve | Előző egyenértékű séma |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Beállítások első része. ConfigurationFunction (előtt \\ \\) |
| settings.configuration.function |Második része a beállításokat. ConfigurationFunction (miután \\ \\) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (nélkül SAS-jogkivonat) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |A protectedSettings.DataBlobUri SAS-jogkivonat |

## <a name="troubleshooting---error-code-1100"></a>Hibaelhárítás – 1100-as hibakód

1100-as hibakód a DSC-bővítmény a felhasználói adatbevitelt tartalmazó problémát jelez.
Ezek a hibák szövegét változik, és előfordulhat, hogy módosítani.
Íme néhány megjelenhet a hibákat, és hogyan oldhatja meg őket.

### <a name="invalid-values"></a>Érvénytelen értékek

"Privacy.dataCollection van"{0}".
Az egyetlen lehetséges értékek: ""% s, "Engedélyezése" és "Disable" ".
"WmfVersion van"{0}".
Csak a lehetséges értékek a következők... "legújabb" és ".

**A probléma**: A megadott érték nem engedélyezett.

**Megoldás**: érvénytelen, hogy módosítja egy érvényes értéket.
További információkért lásd a táblázatot Itt [részletek](#details).

### <a name="invalid-url"></a>Érvénytelen URL

"ConfigurationData.url van"{0}". Ez nem egy érvényes URL-cím"" DataBlobUri van "{0}". Ez nem egy érvényes URL-cím"" Configuration.url van "{0}". Ez nem egy érvényes URL-címe"

**A probléma**: A megadott URL-je nem érvényes.

**Megoldás**: Ellenőrizze a megadott URL-címekhez.
Arról, hogy az összes URL-címeket feloldani érvényes helyekre, hogy a bővítmény hozzáférhet-e a távoli számítógépen.

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument típusa

"Érvénytelen configurationArguments típus {0}"

**A probléma**: A *ConfigurationArguments* tulajdonság nem oldható fel egy **kivonattábla** objektum.

**Megoldás**: Győződjön meg arról, a *ConfigurationArguments* tulajdonság egy **kivonattábla**.
Kövesse az előző példában megadott formátumban. Figyelje meg, az ajánlatok, vesszővel válassza el egymástól, és kapcsos zárójelek.

### <a name="duplicate-configurationarguments"></a>Ismétlődő ConfigurationArguments

"Ismétlődő argumentumok található{0}" a nyilvános, mind a védett configurationArguments "

**A probléma**: A *ConfigurationArguments* nyilvános beállításaiban, és a *ConfigurationArguments* védett beállításaiban tulajdonságok ugyanazzal a névvel rendelkezik.

**Megoldás**: távolítsa el az ismétlődő tulajdonságok egyikét.

### <a name="missing-properties"></a>Hiányzó tulajdonságok

"Configuration.function megköveteli, hogy configuration.url vagy configuration.module van megadva"

"Configuration.url van szükség, hogy configuration.script van megadva."

"Configuration.script van szükség, hogy configuration.url van megadva."

"Configuration.url van szükség, hogy configuration.function van megadva."

"ConfigurationUrlSasToken van szükség, hogy configuration.url van megadva."

"ConfigurationDataUrlSasToken van szükség, hogy configurationData.url van megadva."

**A probléma**: egy meghatározott tulajdonság szüksége van egy másik tulajdonságot használja, amely nincs megadva.

**Megoldások**:

- Adja meg a hiányzó tulajdonság.
- Távolítsa el a tulajdonságot, amelyet a hiányzó tulajdonságot.

## <a name="next-steps"></a>További lépések

- Ismerje meg [virtuális gép méretezési csoportokat használ az Azure DSC bővítménnyel](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- További részleteket talál a kapcsolatos [DSC a biztonságos hitelesítő adatok kezelése](dsc-credentials.md).
- Get- [bemutatása az Azure DSC Bővítménykezelő](dsc-overview.md).
- További információ a PowerShell DSC, nyissa meg a [PowerShell dokumentációs központban](/powershell/dsc/overview).
