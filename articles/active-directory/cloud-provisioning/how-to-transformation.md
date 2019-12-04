---
title: Azure AD Connect felhőalapú kiépítési átalakítások
description: Ez a dokumentum azt ismerteti, hogyan használhatók a transzformációk az alapértelmezett attribútumok leképezésének módosításához.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794470"
---
# <a name="transformations"></a>Átalakítások

Az átalakítással megváltoztathatja az attribútumok az Azure AD-vel való szinkronizálásának alapértelmezett viselkedését a felhőalapú kiépítés használatával.  

A feladat elvégzéséhez szerkesztenie kell a sémát, majd újra el kell küldenie egy webes kérelem használatával.

A felhőalapú kiépítési attribútumokkal kapcsolatos további információkért lásd [Az Azure ad-séma megismerése](concept-attributes.md)című témakört.


## <a name="retrieve-the-schema"></a>Séma beolvasása
A séma beolvasásához hajtsa végre a [séma megtekintése](concept-attributes.md#viewing-the-schema)című témakörben ismertetett lépéseket. 


## <a name="custom-attribute-mapping"></a>Egyéni attribútumok leképezése
Egyéni attribútumok hozzárendelésének hozzáadásához kövesse az alábbi eljárást.

1. Másolja a sémát egy szöveg-vagy Kódszerkesztő-Szerkesztőbe, például a [Visual Studio Code](https://code.visualstudio.com/)-ba.  
2. Keresse meg a sémában frissíteni kívánt objektumot ![](media/how-to-transformation/transform1.png)</br>
3. Keresse meg a felhasználói objektum alatt található **ExtensionAttribute3** kódját.

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
 4.  Szerkessze a kódot úgy, hogy a vállalati attribútum a ExtensionAttribute3 legyen leképezve.
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
 5. Másolja vissza a sémát a Graph Explorerben, módosítsa a kérelem típusát a PUT és a **Run lekérdezésre**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Most a Azure Portal navigáljon a felhő üzembe helyezési konfigurációjához, és **indítsa újra az üzembe**helyezést.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Egy kis idő elteltével ellenőrizze, hogy a következő lekérdezés fut-e a Graph Explorerben: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Ekkor az értéknek kell megjelennie.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Egyéni attribútumok leképezése függvénnyel
A speciális leképezéshez használhat olyan függvényeket, amelyek lehetővé teszik az adatok módosítását és az attribútumok értékének létrehozását a szervezet igényeinek megfelelően.

A feladat elvégzéséhez egyszerűen kövesse a fenti lépéseket, majd szerkessze a végső érték kiépítéséhez használt függvényt.

A szintaxissal és a kifejezésekkel kapcsolatos további információkért lásd: [kifejezések írása az attribútumok megfeleltetéséhez Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
