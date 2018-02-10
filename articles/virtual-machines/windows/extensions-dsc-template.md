---
title: "Kívánt állapot konfigurációs kiterjesztése az Azure Resource Manager-sablonok |} Microsoft Docs"
description: "Kívánt állapot konfigurációs kiterjesztése az Azure Resource Manager-sablon definíciója"
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
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok kívánt állapot konfigurációs kiterjesztése

Ez a cikk ismerteti az Azure Resource Manager sablon a [célállapot-konfiguráció bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Megjegyzés: a némileg eltérő séma példák találkozhat. * 
 *Séma a megváltozott a 2016 októberétől kezdve a kiadási.* 
 *Részletek leírásban ki vannak emelve az ezen a lapon című részében*
*[frissítése az előző formátumból](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>A Windows virtuális gépek sablon – példa

A következő kódrészletet a sablon erőforrás szakasza állapotba kerül.
A DSC-bővítményt örökli az alapértelmezett bővítmény tulajdonságai, ahogy [VirtualMachineExtension osztály](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)

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

## <a name="template-example-for-windows-vmss"></a>A Windows VMSS sablon – példa

Egy VMSS csomópont rendelkezik a "VirtualMachineProfile", "extensionProfile" attribútum a "Tulajdonságok" szakasz. A DSC-ből "kiterjesztésekkel" alatt jelenik meg.

A DSC-bővítményt örökli az alapértelmezett bővítmény tulajdonságai, ahogy [VirtualMachineScaleSetExtension osztály](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

A következő séma egy, az Azure Resource Manager sablon Azure DSC-bővítményt a beállítások része.

*Az alapértelmezett konfigurációs parancsfájl elérhető argumentumok listájának*
*című témakör az alábbi nevű*
*[alapértelmezett konfigurációs parancsfájl](##Default-Configuration-Script) *.

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
| settings.wmfVersion |karakterlánc |Adja meg a Windows Management Framework, amelyet telepíteni kell a virtuális gép verziója. A tulajdonság beállítása "legújabb" telepíti a WMF legfrissebb verzióját. Ez a tulajdonság csak az aktuális lehetséges értékek a következők **"4.0", "5.0", "5.0PP', és a"legutóbbi"**. A lehetséges értékek a frissítések vonatkoznak. Az alapértelmezett érték: "legújabb". |
| settings.configuration.url |karakterlánc |Meghatározza a URL-címét, amelyről letöltheti a DSC konfigurációs zip-fájl. A megadott URL-cím egy SAS-jogkivonat hozzáférést igényel, ha szüksége az SAS-token értékének a protectedSettings.configurationUrlSasToken tulajdonsága. E tulajdonság megadása kötelező, ha settings.configuration.script és/vagy settings.configuration.function vannak meghatározva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítmény fel fogja hívni az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása és argumentumot kell megadni. |
| settings.configuration.script |karakterlánc |Adja meg a parancsfájl a DSC-konfiguráció definícióját tartalmazó fájl neve. Ez a parancsfájl configuration.url tulajdonság által megadott URL-címről letöltött zip-fájl a gyökérmappában kell lennie. E tulajdonság megadása kötelező, ha settings.configuration.url és/vagy settings.configuration.script vannak meghatározva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítmény fel fogja hívni az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása és argumentumot kell alkalmazni. |
| settings.configuration.function |karakterlánc |Megadja a DSC-konfiguráció nevét. A konfiguráció a parancsfájl configuration.script által meghatározott kell tartoznia. E tulajdonság megadása kötelező, ha settings.configuration.url és/vagy settings.configuration.function vannak meghatározva. Ha nincs érték megadva ezekhez a tulajdonságokhoz, a bővítmény fel fogja hívni az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása és argumentumot kell megadni. |
| settings.configurationArguments |Gyűjtemény |Meghatározza bármely továbbítani szeretné a DSC-konfiguráció. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData.url |karakterlánc |Meghatározza a URL-címet, amelyről letöltheti a konfigurációs adatfájlt (.psd1) használandó bemenetként a DSC-konfiguráció. A megadott URL-cím egy SAS-jogkivonat hozzáférést igényel, ha szüksége az SAS-token értékének a protectedSettings.configurationDataUrlSasToken tulajdonsága. |
| settings.privacy.dataEnabled |karakterlánc |Engedélyezheti vagy letilthatja a telemetriai adatok gyűjtése. Ez a tulajdonság csak lehetséges értékei **"Engedélyezés", "Letiltás", ", vagy $null**. Így ez a tulajdonság üres vagy null értékű lehetővé teszi, hogy a telemetriai adatokat. Az alapértelmezett érték ". [További információ](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Gyűjtemény |Határozza meg a másodlagos helyeket, ahonnan letölthető a WMF. [További információ](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Gyűjtemény |Meghatározza bármely továbbítani szeretné a DSC-konfiguráció. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationUrlSasToken |karakterlánc |Megadja a SAS-jogkivonat a configuration.url által megadott URL-cím elérésére. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationDataUrlSasToken |karakterlánc |Megadja a SAS-jogkivonat a configurationData.url által megadott URL-cím elérésére. Ez a tulajdonság titkosítva van. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Ezeket az értékeket további információkért lásd: a dokumentációs oldal [helyi Configuration Manager alapbeállítások](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
Csak a lenti táblázatban szereplő LCM tulajdonságok esetében konfigurálhatók a DSC-bővítményt alapértelmezett konfigurációs parancsfájl használata.

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Kötelező tulajdonság. Adja meg az Azure Automation szolgáltatás regisztrálása és a PowerShell-hitelesítő adat objektum egy csomópont használt kulcs. Ezt az értéket automatikusan észlelhetők az listkeys metódussal az Automation-fiók ellen, és a védett beállításként titkosítani kell. |
| settings.configurationArguments.RegistrationUrl |karakterlánc |Kötelező tulajdonság. Megadja a Ha a csomópont megkísérli regisztrálni Azure-automatizálás végpontjának URL-címét. Ezt az értéket automatikusan észlelhetők az Automation-fiók ellen hivatkozás metódussal. |
| settings.configurationArguments.NodeConfigurationName |karakterlánc |Kötelező tulajdonság. Meghatározza a csomópont-konfigurációnak a csomópont hozzárendelése az Azure Automation-fiók. |
| settings.configurationArguments.ConfigurationMode |karakterlánc |Meghatározza a módját a helyi Configuration Manager számára. Érvényes lehetőségek a következők: "ApplyOnly", "ApplyandMonitor" és "ApplyandAutoCorrect".  Az alapértelmezett érték: "ApplyandMonitor". |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Itt adhatja meg, milyen gyakran LCM megkísérli egyeztetni az Automation-fiók a frissítéseket.  Alapértelmezett érték 30.  Minimális érték 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Itt adhatja meg, milyen gyakran LCM ellenőrzi az aktuális konfigurációt.  Alapértelmezett érték 15.  Minimális érték 15. |
| settings.configurationArguments.RebootNodeIfNeeded | logikai | Meghatározza, hogy egy csomópont lehet, hogy újra kell automatikusan indítani egy DSC művelet kérésére.  Alapértelmezett értéke hamis. |
| settings.configurationArguments.ActionAfterReboot | karakterlánc | Itt adhatja meg, mi történik, a rendszer újraindítása után a konfiguráció alkalmazása során. Érvényes értékek: "ContinueConfiguration" és "StopConfiguration". Alapértelmezett érték: "ContinueConfiguration". |
| settings.configurationArguments.AllowModuleOverwrite | logikai | Meghatározza, hogy LCM felülírja a meglévő modulok a csomóponton.  Alapértelmezett értéke hamis. |

## <a name="settings-vs-protectedsettings"></a>Vs beállításait. ProtectedSettings

Minden beállítások mentése beállítások a fájlt a virtuális Gépen.
A "beállítások" olyan nyilvános tulajdonságok, mert az szöveges fájlban nincsenek titkosítva.
A "protectedSettings" tulajdonságok a tanúsítvánnyal titkosított, és nem jelenítjük meg egyszerű szöveges ebben a fájlban, a virtuális Gépen.

Ha a konfiguráció a hitelesítő adatokat igényel, protectedSettings is szerepelni:

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

## <a name="example"></a>Példa

A következő példa a DSC-bővítményt, amely adja meg a metaadat-beállításokat a helyi Configuration Manager és az Azure Automation DSC szolgáltatással regisztrálása az alapértelmezett működést.
Konfigurációs argumentumok szükségesek, és az alapértelmezett konfigurációs parancsfájl LCM metaadatok beállítása átadandó.

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

## <a name="example-using-configuration-script-in-azure-storage"></a>Konfigurációs parancsfájl használata az Azure Storage – példa

A következő példa az "Első lépések" szakaszából származik a [DSC kiterjesztés kezelője – áttekintés oldalra](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
A példa Resource Manager-sablonok parancsmagok helyett a bővítmény telepítéséhez.
A "IisInstall.ps1" konfigurációs elmentéséhez elhelyezéséhez egy. A ZIP-fájl, és töltse fel a fájlt egy elérhető URL-CÍMÉT.
A példában az Azure blob Storage tárolóban, de lehet letölteni. A ZIP-fájlok bármilyen tetszőleges helyről.

Az Azure Resource Manager sablon a következő kódot arra utasítja a virtuális gép a megfelelő fájl letöltése és futtatása a megfelelő PowerShell függvényt:

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

## <a name="updating-from-the-previous-format"></a>Az előző formátumból frissítése

Az előző formátum (ModulesUrl, ConfigurationFunction, SasToken vagy tulajdonságok nyilvános tulajdonságait tartalmazó) automatikusan beállításait alkalmazkodnak hozzá a formátumban, és mint előtt futtassa.

A következő séma milyen az előző beállítások séma hasonlóan keresni:

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

| Tulajdonság neve | Előző séma megfelelője |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Beállítások első része. ConfigurationFunction (előtt "\\\\") |
| settings.configuration.function |Második része a beállításokat. ConfigurationFunction (után "\\\\") |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (nélkül SAS-jogkivonat) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS token from protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Hibaelhárítás – 1100-as hibakód

1100-as hibakód azt jelzi, hogy a felhasználó által megadott a DSC-bővítmény probléma van.
Ezek a hibák szövegét változó, és módosíthatja.
Íme néhány a hibákat, azokat is futtathatja, és hogyan oldhatja meg őket.

### <a name="invalid-values"></a>Érvénytelen értékekkel

"Privacy.dataCollection nem"{0}".
Az egyetlen lehetséges értékek: ","Engedélyezése"és"Disable"".
"WmfVersion nem"{0}".
Csak a lehetséges értékek a következők... "legújabb" és ".

Probléma: A megadott érték nem megengedett.

Megoldás: Módosítsa a érvénytelen érvényes értéket.
A táblázatban a Részletek területen találja.

### <a name="invalid-url"></a>Érvénytelen URL

"ConfigurationData.url nem"{0}". Ez nem egy érvényes URL-címet az""DataBlobUri nem "{0}". Ez nem egy érvényes URL-címet az""Configuration.url nem "{0}". Ez nem egy érvényes URL-címet az"

Probléma: A megadott URL-cím érvénytelen.

Megoldás: Ellenőrizze az a megadott URL-címet.
Győződjön meg arról, hogy az URL-címet oldható fel érvényes helyekre, hogy a kiterjesztés hozzáférhet-e a távoli számítógépen.

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument típusa

"Típus érvénytelen configurationArguments" {0}

Probléma: A ConfigurationArguments tulajdonság nem oldható fel egy kivonattáblát objektum.

Megoldás: Ellenőrizze a ConfigurationArguments tulajdonság egy kivonattáblát.
A megadott portáladatbázis előző példában formátumot követi.
Figyeljen az ajánlatok, vesszővel válassza el egymástól, és kell használni.

### <a name="duplicate-configurationarguments"></a>Ismétlődő ConfigurationArguments

"Található nyilvános és a védett configurationArguments ismétlődő argumentumok"{0}""

Probléma: A nyilvános beállításai ConfigurationArguments és a védett beállításai ConfigurationArguments tulajdonságot tároljon ugyanazzal a névvel.

Megoldás: Távolítsa el az ismétlődő tulajdonságok egyikét.

### <a name="missing-properties"></a>Hiányzó tulajdonságok
"Megköveteli, hogy configuration.url configuration.module meg van adva vagy Configuration.function"

"Megköveteli, hogy configuration.script megadott Configuration.url"

"Megköveteli, hogy configuration.url megadott Configuration.script"

"Megköveteli, hogy configuration.function megadott Configuration.url"

"Megköveteli, hogy configuration.url megadott ConfigurationUrlSasToken"

"Megköveteli, hogy configurationData.url megadott ConfigurationDataUrlSasToken"

Probléma: Egy meghatározott tulajdonság kell, hogy hiányzik egy másik tulajdonságot.

Megoldások:

- Adja meg a hiányzó tulajdonság.
- Távolítsa el azt a tulajdonságot, amelyet a hiányzó tulajdonság.

## <a name="next-steps"></a>További lépések

További tudnivalók a DSC-ből és a virtuálisgép-skálázási készletekben [használó virtuálisgép-skálázási készletekben Azure DSC kiterjesztésű](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

További részletekért található a következő [DSC biztonságos hitelesítőadat-kezelés](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Az Azure DSC-bővítmény kezelő további információkért lásd: [bemutatása az Azure célállapot-konfiguráció bővítmény kezelő](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

További információ a PowerShell DSC [látogasson el a PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview).
