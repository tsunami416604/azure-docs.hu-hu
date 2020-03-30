---
title: Az Azure AD Connect felhőalapú kiépítési átalakításai
description: Ez a cikk azt ismerteti, hogy miként lehet átalakítások segítségével módosítani az alapértelmezett attribútumleképezéseket.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549295"
---
# <a name="transformations"></a>Átalakítások

Az átalakítással módosíthatja az azure Active Directoryval (Azure AD) való attribútumszinkronizálás alapértelmezett viselkedését a felhőalapú kiépítés használatával.

A feladat végrehajtásához szerkesztenikell a sémát, majd újra el kell küldenie egy webes kérelemen keresztül.

A felhőalapú kiépítési attribútumokkal kapcsolatos további információkért [lásd: Az Azure AD-séma ismertetése.](concept-attributes.md)


## <a name="retrieve-the-schema"></a>A séma beolvasása
A séma beolvasásához kövesse [a Séma megtekintése](concept-attributes.md#view-the-schema)című részben leírt lépéseket. 

## <a name="custom-attribute-mapping"></a>Egyéni attribútumleképezés
Egyéni attribútumleképezés hozzáadásához kövesse az alábbi lépéseket.

1. Másolja a sémát egy szövegbe vagy kódszerkesztőbe, például [a Visual Studio-kódba.](https://code.visualstudio.com/)
1. Keresse meg a sémában frissíteni kívánt objektumot.

   ![Objektum a sémában](media/how-to-transformation/transform1.png)</br>
1. Keresse meg `ExtensionAttribute3` a felhasználói objektum alatti kódot.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. A kód szerkesztése úgy, hogy a vállalat `ExtensionAttribute3`attribútuma hozzá legyen rendelve.

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Másolja vissza a sémát a Graph Explorer programba, módosítsa a **kérelem típusát** **PUT -ra,** és válassza a **Lekérdezés futtatása**lehetőséget.

    ![Lekérdezés futtatása](media/how-to-transformation/transform2.png)

 1. Most az Azure Portalon lépjen a felhőkiépítési konfigurációra, és válassza **a Kiépítés újraindítása**lehetőséget.

    ![Kiépítés újraindítása](media/how-to-transformation/transform3.png)

 1. Egy idő után ellenőrizze, hogy az attribútumok feltöltése `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`a következő lekérdezés futtatásával van-e feltöltve a Graph Explorer programban: .
 1. Most látnia kell az értéket.

    ![Az érték megjelenik](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Egyéni attribútumleképezés funkcióval
A fejlettebb leképezéshez olyan függvényeket használhat, amelyek lehetővé teszik az adatok manipulálását és a szervezet igényeinek megfelelő attribútumok értékeinek létrehozását.

A feladat végrehajtásához hajtsa végre az előző lépéseket, majd szerkesztse a végső érték létrehozásához használt függvényt.

A szintaxisról és a kifejezések példáiról az [Attribútumleképezések kifejezések írása az Azure Active Directoryban](reference-expressions.md)című témakörben talál további információt.


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
