---
title: Azure AD Connect felhőalapú kiépítési átalakítások
description: Ez a cikk azt ismerteti, hogyan használhatók a transzformációk az alapértelmezett attribútumok leképezésének módosításához.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: a15a25ce4fbe1a43c2a5065bf88f5c40c5865721
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85360690"
---
# <a name="transformations"></a>Átalakítások

Az átalakítással megváltoztathatja az attribútumok Azure Active Directory (Azure AD) szolgáltatással való szinkronizálásának alapértelmezett viselkedését a Felhőbeli kiépítés használatával.

A feladat elvégzéséhez szerkesztenie kell a sémát, majd újra el kell küldenie egy webes kérelem használatával.

A felhőalapú kiépítési attribútumokkal kapcsolatos további információkért lásd [Az Azure ad-séma megismerése](concept-attributes.md)című témakört.


## <a name="retrieve-the-schema"></a>Séma beolvasása
A séma beolvasásához kövesse a [séma megtekintése](concept-attributes.md#view-the-schema)című témakör lépéseit. 

## <a name="custom-attribute-mapping"></a>Egyéni attribútumok leképezése
Egyéni attribútumok hozzárendelésének hozzáadásához kövesse az alábbi lépéseket.

1. Másolja a sémát egy szöveg-vagy Kódszerkesztő-Szerkesztőbe, például a [Visual Studio Code](https://code.visualstudio.com/)-ba.
1. Keresse meg a sémában frissíteni kívánt objektumot.

   ![Objektum a sémában](media/how-to-transformation/transform1.png)</br>
1. Keresse meg a `ExtensionAttribute3` felhasználói objektum alatt található kódot.

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
1. Szerkessze a kódot úgy, hogy a vállalati attribútum le legyen képezve `ExtensionAttribute3` .

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
 1. Másolja vissza a sémát a Graph Explorerben, módosítsa a **kérelem típusát** a **put**értékre, majd válassza a **lekérdezés futtatása**lehetőséget.

    ![Lekérdezés futtatása](media/how-to-transformation/transform2.png)

 1. Most a Azure Portal nyissa meg a felhő üzembe helyezési konfigurációját, és válassza a **kiépítés újraindítása**lehetőséget.

    ![Kiépítés újraindítása](media/how-to-transformation/transform3.png)

 1. Egy kis idő elteltével ellenőrizze, hogy az attribútumok feltöltése a következő lekérdezés futtatásával történik-e a Graph Explorerben: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Ekkor az értéknek kell megjelennie.

    ![Az érték jelenik meg](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Egyéni attribútumok leképezése függvénnyel
A speciális leképezéshez használhatja azokat a függvényeket, amelyek lehetővé teszik az adatok módosítását és az attribútumok értékének létrehozását a szervezet igényeinek megfelelően.

A feladat elvégzéséhez kövesse az előző lépéseket, majd szerkessze a végső érték létrehozásához használt függvényt.

A szintaxissal és a kifejezésekkel kapcsolatos további információkért lásd: [kifejezések írása az attribútumok megfeleltetéséhez Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
