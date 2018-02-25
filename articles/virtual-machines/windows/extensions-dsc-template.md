---
title: "Állapotkonfiguráció bővítmény Azure Resource Manager-sablonok szükséges |} Microsoft Docs"
description: "Ismerje meg a kívánt állapot konfigurációs szolgáltatása (DSC) bővítmény, az Azure Resource Manager sablon definíciója."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: 0f1c53c9eafcd96e49232b75d46ef34537a1160f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok kívánt állapot konfigurációs bővítmény

Ez a cikk ismerteti az Azure Resource Manager sablon a [kívánt állapot konfigurációs szolgáltatása (DSC) bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

> [!NOTE]
> Némileg eltérő séma példák léphetnek fel. A séma megváltozott a 2016. októberi kiadás. További információkért lásd: [frissítés az előző formátumból](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>A Windows virtuális gépek sablon – példa

A következő kódrészletet mutat a **erőforrás** a sablon szakasza. A DSC-bővítményt alapértelmezett bővítmény tulajdonságokat örökli. További információkért lásd: [VirtualMachineExtension osztály](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Windows virtuálisgép-méretezési sablon példa beállítása

A virtuális gép méretezési készlet fürtcsomópont egy **tulajdonságok** szakaszt, amely rendelkezik egy **VirtualMachineProfile, extensionProfile** attribútum. A **bővítmények**, vegye fel a DSC-ből.

A DSC-bővítményt alapértelmezett bővítmény tulajdonságokat örökli. További információkért lásd: [VirtualMachineScaleSetExtension osztály](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Részletes beállítási információk

A következő séma használata a **beállítások** a Resource Manager-sablon Azure DSC-bővítmény szakasza.

A következő paraméterek érhetők el az alapértelmezett konfigurációs parancsfájl listájáért lásd: [konfigurációs parancsfájl alapértelmezett](#default-configuration-script).

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
| settings.wmfVersion |karakterlánc |Megadja a verzióját a Windows Management Framework (WMF), amely a virtuális Gépre kell telepíteni. Ez a tulajdonság **legújabb** WMF legújabb verzióját telepíti. Ez a tulajdonság csak lehetséges értékei jelenleg **4.0**, **5.0**, **5.0PP**, és **legújabb**. A lehetséges értékek a frissítések vonatkoznak. Az alapértelmezett érték **legújabb**. |
| settings.configuration.url |karakterlánc |Meghatározza a URL-címét, amelyről letöltheti a DSC-konfiguráció .zip fájlt. Ha az URL-cím egy SAS-jogkivonat hozzáférést igényel, állítsa be a **protectedSettings.configurationUrlSasToken** tulajdonság a SAS-jogkivonat értékével. E tulajdonság megadása kötelező, ha **settings.configuration.script** vagy **settings.configuration.function** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl helyét a Configuration Manager (LCM) metaadatok beállítani, és argumentumot kell megadni. |
| settings.configuration.script |karakterlánc |Adja meg a parancsfájl a DSC-konfiguráció definícióját tartalmazó fájl neve. Ez a parancsfájl által megadott URL-címről letöltött .zip fájl a gyökérmappában kell lennie a **configuration.url** tulajdonság. E tulajdonság megadása kötelező, ha **settings.configuration.url** vagy **settings.configuration.script** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítani, és argumentumot kell megadni. |
| settings.configuration.function |karakterlánc |Megadja a DSC-konfiguráció nevét. A konfigurációs nevű szerepelnie kell a parancsfájlt, amely **configuration.script** határozza meg. E tulajdonság megadása kötelező, ha **settings.configuration.url** vagy **settings.configuration.function** vannak definiálva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítményt meghívja az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítani, és argumentumot kell megadni. |
| settings.configurationArguments |Gyűjtemény |Határozza meg a paramétereket, szeretné továbbítani a DSC-konfiguráció. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData.url |karakterlánc |Meghatározza a URL-címet, amelyről letöltheti a konfigurációs adatfájlt (.psd1) használandó bemenetként a DSC-konfiguráció. Ha az URL-cím egy SAS-jogkivonat hozzáférést igényel, állítsa be a **protectedSettings.configurationDataUrlSasToken** tulajdonság a SAS-jogkivonat értékével. |
| settings.privacy.dataEnabled |karakterlánc |Engedélyezheti vagy letilthatja a telemetriai adatok gyűjtése. Ez a tulajdonság csak lehetséges értékei **engedélyezése**, **letiltása**, **"**, vagy **$null**. Így ez a tulajdonság üres vagy null értékű lehetővé teszi, hogy a telemetriai adatokat. Az alapértelmezett érték **"**. További információkért lásd: [Azure DSC-bővítmény adatgyűjtés](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Gyűjtemény |Határozza meg a másodlagos helyeket, ahonnan letölthető a WMF. További információkért lásd: [2.8 Azure DSC-bővítményt, és hogyan képezheti bővítmény függőség letöltések saját](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Gyűjtemény |Határozza meg a paramétereket, szeretné továbbítani a DSC-konfiguráció. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationUrlSasToken |karakterlánc |Megadja a SAS-jogkivonat használatával férjenek hozzá az URL-címet, amely **configuration.url** határozza meg. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationDataUrlSasToken |karakterlánc |Megadja a SAS-jogkivonat használatával férjenek hozzá az URL-címet, amely **configurationData.url** határozza meg. Ez a tulajdonság titkosítva van. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

A következő értékek kapcsolatos további információkért lásd: [helyi Configuration Manager alapbeállítások](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings). A DSC-bővítmény alapértelmezett konfigurációs parancsfájl segítségével csak a felsorolt LCM tulajdonságainak konfigurálása a következő táblázat az.

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Kötelező tulajdonság. Megadja a kulcsot, amellyel egy csomópont és a PowerShell-hitelesítő adat objektum az Azure Automation szolgáltatás regisztrálása. Ezt az értéket automatikusan észlelhetők használatával a **listkeys** metódus az Automation-fiók ellen. Az érték egy védett beállítás titkosítani kell. |
| settings.configurationArguments.RegistrationUrl |karakterlánc |Kötelező tulajdonság. Adja meg a-automatizálás végpontjának, ahol a csomópontra megkísérel regisztrálni URL-CÍMÉT. Ezt az értéket automatikusan észlelhetők használatával a **hivatkozás** metódus az Automation-fiók ellen. |
| settings.configurationArguments.NodeConfigurationName |karakterlánc |Kötelező tulajdonság. Meghatározza a csomópont-konfiguráció hozzárendelése a csomópont Automation-fiók. |
| settings.configurationArguments.ConfigurationMode |karakterlánc |Meghatározza a mód LCM. Érvényes lehetőségek a következők **ApplyOnly**, **ApplyandMonitor**, és **ApplyandAutoCorrect**.  Az alapértelmezett érték **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Itt adhatja meg, milyen gyakran kíséri meg LCM lépjen kapcsolatba az Automation-fiók a frissítéseket.  Alapértelmezett érték **30**.  Minimális érték **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Itt adhatja meg, milyen gyakran LCM ellenőrzi az aktuális konfigurációt. Alapértelmezett érték **15**. Minimális érték **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | logikai | Meghatározza, hogy a csomópont automatikusan az OK gombra a DSC-művelet kérésére. Alapértelmezett érték **hamis**. |
| settings.configurationArguments.ActionAfterReboot | karakterlánc | Itt adhatja meg, mi történik, a rendszer újraindítása után a konfiguráció alkalmazása során. Az érvényes beállítások: **ContinueConfiguration** és **StopConfiguration**. Alapértelmezett érték **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | logikai | Meghatározza, hogy LCM felülírja a meglévő modulok a csomóponton. Alapértelmezett érték **hamis**. |

## <a name="settings-vs-protectedsettings"></a>Vs beállításait. ProtectedSettings

Minden beállítások mentése beállítások a fájlt a virtuális Gépen. A felsorolt tulajdonságok **beállítások** nyilvános tulajdonságokat. Nyilvános tulajdonságok a beállítások szövegfájlban nincsenek titkosítva. A felsorolt tulajdonságok **protectedSettings** a tanúsítvánnyal titkosított, és nem jelenítjük meg egyszerű szövegként a virtuális Gépre, a beállítások fájlban.

Ha a konfiguráció a hitelesítő adatokat igényel, a felhasználó hitelesítő adatait is felvehet **protectedSettings**:

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

A következő példa bemutatja a DSC-bővítményt, amely metaadat-beállításai LCM adja meg, és regisztrálja az Automation DSC szolgáltatással alapértelmezett viselkedése. Konfigurációs argumentumok megadása kötelező.  Konfigurációs argumentumok továbbítódnak az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállításához.

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
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>A konfigurációs parancsfájl használata az Azure Storage – példa

A rendszer a következő példa a [DSC-bővítmény kezelő áttekintése](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A példa Resource Manager-sablonok parancsmagok helyett a bővítmény telepítéséhez. A IisInstall.ps1 konfiguráció mentéséhez, és helyezheti el egy .zip fájl majd töltse fel a fájlt egy elérhető URL-CÍMÉT. A példában az Azure Blob Storage tárolóban, de a .zip-fájlt tölthet le bármely tetszőleges helyre.

A Resource Manager sablon a következő kódot arra utasítja a virtuális Gépet, és töltse le a megfelelő fájlt, majd futtassa a megfelelő PowerShell függvényt:

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

## <a name="update-from-a-previous-format"></a>Előző formátumból frissítése

A bővítmény előző formátum tartozó egyik beállítás (és a nyilvános jellemzőkkel rendelkezik, amely **ModulesUrl**, **ConfigurationFunction**, **SasToken**, vagy  **Tulajdonságok**) automatikusan alkalmazkodnak hozzá a bővítmény formátumban. A Futtatás mint előtt.

A következő séma bemutatja, milyen az előző beállítási sémában például keresést végrehajtani:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
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

Ez a korábbi formátum hogyan alkalmazkodik a formátumban:

| Tulajdonság neve | Előző séma egyenértékű |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Beállítások első része. ConfigurationFunction (előtt \\ \\) |
| settings.configuration.function |Második része a beállításokat. ConfigurationFunction (után \\ \\) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (nélkül SAS-jogkivonat) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS token from protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Hibaelhárítás – 1100-as hibakód

1100-as hibakód azt jelzi, hogy a felhasználó által megadott a DSC-bővítmény probléma. Ezek a hibák szövegét változik, és előfordulhat, hogy módosítani. Íme néhány a hibák mutatjuk be, és hogyan oldhatja meg őket.

### <a name="invalid-values"></a>Érvénytelen értékekkel

"Privacy.dataCollection nem"{0}".
Az egyetlen lehetséges értékek: ","Engedélyezése"és"Disable"".
"WmfVersion nem"{0}".
Csak a lehetséges értékek a következők... "legújabb" és ".

**A probléma**: A megadott érték nem megengedett.

**Megoldás**: módosítsa a érvénytelen érvényes értéket. További információkért lásd: a táblázatban szereplő [részletek](#details).

### <a name="invalid-url"></a>Érvénytelen URL

"ConfigurationData.url nem"{0}". Ez nem egy érvényes URL-címet az""DataBlobUri nem "{0}". Ez nem egy érvényes URL-címet az""Configuration.url nem "{0}". Ez nem egy érvényes URL-címet az"

**A probléma**: A megadott URL-cím érvénytelen.

**Megoldás**: Ellenőrizze az a megadott URL-címet. Győződjön meg arról, hogy az URL-címet oldható fel érvényes helyekre, hogy a kiterjesztés hozzáférhet-e a távoli számítógépen.

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument típusa

"Típus érvénytelen configurationArguments" {0}

**A probléma**: A *ConfigurationArguments* tulajdonság nem oldható fel egy **Hashtable** objektum.

**Megoldás**: Ellenőrizze a *ConfigurationArguments* tulajdonság egy **Hashtable**. Kövesse az előző példában megadott formátumban. Figyelendő ajánlatok, vesszővel válassza el egymástól, és kell használni.

### <a name="duplicate-configurationarguments"></a>Ismétlődő ConfigurationArguments

"Található nyilvános és a védett configurationArguments ismétlődő argumentumok"{0}""

**A probléma**: A *ConfigurationArguments* nyilvános beállításai és a *ConfigurationArguments* a védett beállításai tulajdonság azonos névvel rendelkezik.

**Megoldás**: távolítsa el az ismétlődő tulajdonságok egyikét.

### <a name="missing-properties"></a>Hiányzó tulajdonságok
"Megköveteli, hogy configuration.url configuration.module meg van adva vagy Configuration.function"

"Megköveteli, hogy configuration.script megadott Configuration.url"

"Megköveteli, hogy configuration.url megadott Configuration.script"

"Megköveteli, hogy configuration.function megadott Configuration.url"

"Megköveteli, hogy configuration.url megadott ConfigurationUrlSasToken"

"Megköveteli, hogy configurationData.url megadott ConfigurationDataUrlSasToken"

**A probléma**: kell egy másik tulajdonság, amely nem található A megadott tulajdonságot.

**Megoldások**:

- Adja meg a hiányzó tulajdonság.
- Távolítsa el azt a tulajdonságot, amelyet a hiányzó tulajdonság.

## <a name="next-steps"></a>További lépések

* További tudnivalók [virtuálisgép-méretezési használatával beállítja a Azure DSC-bővítménnyel](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
* Kapcsolatos további részletekért található [DSC biztonságos hitelesítőadat-kezelés](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Első egy [bemutatása az Azure DSC-bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A PowerShell DSC kapcsolatos további információkért lásd a [PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview).
