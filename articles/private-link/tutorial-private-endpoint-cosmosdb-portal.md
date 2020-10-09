---
title: 'Oktatóanyag: Kapcsolódás Azure Cosmos-fiókhoz Azure Private-végpont használatával'
titleSuffix: Azure Private Link
description: Ismerkedés az Azure Private-végponttal egy Azure Cosmos-fiókhoz való nyilvános kapcsolódáshoz.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 8b38c72efff5b76392d23837696c340e3cfb58de
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844251"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Oktatóanyag: Kapcsolódás Azure Cosmos-fiókhoz Azure Private-végpont használatával

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy Cosmos DB fiókot egy privát végponttal.
> * Cosmos DB fiók magánhálózati végpontjának kapcsolatának tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Régió           | Válassza ki az **USA keleti** régióját |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

6. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MySubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Válassza a **Mentés** lehetőséget.

8. Válassza a **Biztonság** fület.

9. A **BastionHost**területen válassza az **Engedélyezés**lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név**mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális gépet, amely a privát végpont tesztelésére szolgál.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** vagy a keresés a **virtuális gépen** elemet a keresőmezőbe.
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Régió | Válassza ki az **USA keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | Válassza a **Windows Server 2019 Datacenter – Gen1** elemet. |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **mySubnet** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | **Basic**|
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Cosmos DB-fiók létrehozása privát végponttal

Ebben a szakaszban létrehoz egy Cosmos DB fiókot, és konfigurálja a magánhálózati végpontot.

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **Cosmos db fiók**lehetőséget, vagy keresse meg a **Cosmos db fiókot** a keresőmezőbe.

2. Az **Cosmos db fiók létrehozása** **alapismeretek** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** |  |
    | Fióknév | Adja meg a **mycosmosdb**. Ha a név nem érhető el, adjon meg egy egyedi nevet. |
    | API | Válassza a **Mag (SQL)** lehetőséget. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget. |
    | Kapacitás mód | Hagyja meg az alapértelmezett **kiépített átviteli sebességet**. |
    | Az ingyenes szint árengedményének alkalmazása | Hagyja meg az alapértelmezett nem **érvényes**beállítást. |
    | Georedundancia | Hagyja meg az alapértelmezett **letiltást**. |
    | Többrégiós írók | Hagyja meg az alapértelmezett **letiltást**. |
   
3. Válassza a **hálózatkezelés** fület, vagy kattintson a **Tovább: hálózatkezelés** gombra.

4. A **hálózatkezelés** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózati kapcsolat** | |
    | Kapcsolati mód | Válasszon **privát végpontot**. |
    | **Tűzfal konfigurálása** | |
    | Hozzáférés engedélyezése a Azure Portal | Hagyja meg az alapértelmezett **engedélyezést**. |
    | Hozzáférés engedélyezése az IP-címről | Hagyja meg az alapértelmezett **Megtagadás**értéket. |

5. A **privát végponton**válassza a **+ Hozzáadás**lehetőséget.

6. A **privát végpont létrehozása** területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | Hely | Válassza ki az **USA keleti** régióját |
    | Name (Név) | **MyPrivateEndpoint** megadása |
    | Cél alerőforrás | Hagyja meg az alapértelmezett **mag (SQL)** |
    | **Hálózat** |  |
    | Virtuális hálózat | **MyVNet** kiválasztása |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | **saját DNS integráció** |
    | Integrálás saját DNS-zónával | Hagyja meg az alapértelmezett **Igen értéket** |
    | Privát DNS-zóna | Hagyja meg az alapértelmezett (új) privatelink.documents.azure.com |

7. Válassza az **OK** lehetőséget.

8. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

9. Válassza a **Létrehozás** lehetőséget.

### <a name="add-a-database-and-a-container"></a>Adatbázis és tároló hozzáadása

1. Válassza ki az **erőforrást** , vagy a Azure Portal bal oldali menüjében válassza a **minden erőforrás**  >  **mycosmosdb**lehetőséget.

2. A bal oldali menüben válassza a **adatkezelő**lehetőséget.

3. Az **adatkezelő** ablakban válassza az **új tároló**elemet.

4. A **tároló hozzáadása**lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Adatbázis-azonosító | Hagyja meg az alapértelmezett **Create New (új létrehozása**) beállítást. </br> Írja be a **mydatabaseid** szöveget a szövegmezőbe. |
    | Átviteli sebesség (400-100 000 RU/s) | Hagyja meg az alapértelmezett **manuális**beállítást. </br> Adja meg a **400** értéket a szövegmezőben. |
    | Tároló azonosítója | **Mycontainerid** megadása |
    | Partíciókulcs | **/Mykey** megadása |

5. Válassza az **OK** lehetőséget.

10. A CosmosDB-fiók **Beállítások** szakaszában válassza a **kulcsok**elemet.

11. Válassza a **myResourceGroup** lehetőséget.

12. Válassza ki az előző lépésekben létrehozott Storage-fiókot.

14. Válassza a másolás lehetőséget az **elsődleges kapcsolatok karakterláncán**.

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni a Cosmos DB-fiókhoz való kapcsolódáshoz a privát végponton keresztül.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza a **myVM**lehetőséget.

4. A **myVM**áttekintés lapján válassza a **kapcsolat** , majd a **megerősített**lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

8. Írja be a következő szöveget: `nslookup <storage-account-name>.documents.azure.com`. Cserélje le az **\<storage-account-name>** nevet az előző lépésekben létrehozott Storage-fiók nevére. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```

    A rendszer a **10.1.0.5** magánhálózati IP-címét adja vissza a Cosmos db fiók nevéhez.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

9. Telepítse a [Microsoft Azure Storage Explorert](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows) a virtuális gépre.

10. A **Microsoft Azure Storage Explorer** telepítése után válassza a **Befejezés** lehetőséget.  Az alkalmazás megnyitásához hagyja bejelölve a jelölőnégyzetet.

11. A **Kapcsolódás az Azure Storage-hoz** képernyőn válassza a **Mégse**lehetőséget.

12. Storage Explorer kattintson a jobb gombbal az **Cosmos db fiókok** elemre, majd válassza **a Kapcsolódás a**következőhöz Cosmos db lehetőséget.

13. Hagyja meg az alapértelmezett **SQL** -t a **Select API**alatt.

14. Illessze be az előző lépésekben másolt Cosmos DB fiókból a **kapcsolatok karakterlánca alatt található**mezőbe.

15. Kattintson a **Tovább** gombra.

16. Ellenőrizze, hogy helyesek-e a beállítások a **kapcsolatok összegzésében**.  

17. Kattintson a **Csatlakozás** gombra.

18. A **myVM**létesített kapcsolatok lezárása.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a Cosmos DB fiókot a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **myResourceGroup** **nevet az erőforráscsoport neve mezőbe**.

5. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hozta létre:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Cosmos DB fiók.

Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást:
> [!div class="nextstepaction"]
> [Privát kapcsolati szolgáltatás létrehozása](create-private-link-service-portal.md)