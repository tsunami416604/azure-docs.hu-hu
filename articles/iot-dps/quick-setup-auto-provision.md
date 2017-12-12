---
title: "Eszközkiépítés beállítása az Azure Portalon | Microsoft Docs"
description: "Azure gyors üzembe helyezés – Azure IoT Hub eszközkiépítési szolgáltatás üzembe helyezése az Azure Portalon"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 59acc48870adf15f59391de1cba9596744a747a6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal

Ezek a lépések azt mutatják be, hogyan történik az Azure felhőbeli erőforrások üzembe helyezése a portálon az eszközkiépítéshez. Ennek során megtörténik az IoT Hub és egy új IoT Hub eszközkiépítési szolgáltatás létrehozása, illetve e két szolgáltatás összekapcsolása. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Válassza az **Eszközök internetes hálózata**, majd az **IoT Hub** elemeket, végül pedig kattintson a **Létrehozás** gombra. 

3. Adjon meg egy **nevet** az IoT Hub számára. Válasszon a rendelkezésre álló díjszabási lehetőségek közül, adja meg az [IoT Hub-egységek](https://azure.microsoft.com/pricing/details/iot-hub/) értékét, válassza ki az eszközről a felhőbe irányuló üzenetek partícióinak számát, illetve az erőforráshoz használni kívánt előfizetést. Adja meg egy új vagy egy meglévő erőforráscsoport nevét, és válassza ki annak helyét. Amikor végzett, kattintson a **Létrehozás** gombra.

    ![Írja be az IoT Hub alapvető adatait a portálpanelen](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. Az IoT Hub sikeres üzembe helyezése után automatikusan megnyílik a hub összefoglalás panelje.


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Új példány létrehozása az IoT Hub eszközkiépítési szolgáltatáshoz

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. *Keresse meg a Marketplace piactéren* az **eszközkiépítési szolgáltatást**. Válassza az **IoT eszközkiépítési szolgáltatás** elemet, és kattintson a **Létrehozás** gombra. 

3. **Nevezze el** az eszközkiépítési szolgáltatás példányát. Válassza ki az ehhez a példányhoz használni kívánt előfizetést, és nevezzen meg egy új vagy létező erőforráscsoportot. Válassza ki a helyet. Amikor végzett, kattintson a **Létrehozás** gombra.

    ![Írja be a DPS példány alapvető adatait a portálpanelen](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. A szolgáltatás sikeres üzembe helyezése után automatikusan megnyílik annak összefoglalás panelje.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

1. Kattintson az Azure Portal bal oldali menüjében lévő **Összes erőforrás** gombra. Válassza ki az előző szakaszban létrehozott eszközkiépítési szolgáltatás példányt.  

2. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Csatolt IoT Hubok** lehetőséget. Kattintson a felül látható **+ Hozzáadás** gombra. 

3. A **Hivatkozás hozzáadása IoT Hubhoz** portálpanelen válassza ki az aktuális előfizetést, vagy írja be egy másik előfizetés nevét és kapcsolati karakterláncát. Válassza ki a hub nevét a legördülő listából. Amikor végzett, kattintson a **Mentés** gombra. 

    ![A hub nevének csatolása a DPS példányhoz a portálpanelen](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Most látnia kell a kiválasztott hubot a **Csatolt IoT Hubok** panelen. 



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** panel felső részén kattintson a **Törlés** elemre.  

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban IoT Hubot és eszközkiépítési szolgáltatás példányt helyezett üzembe, és csatolta a két erőforrást. Ha szimulált eszköz kiépítéséhez szeretné használni ezt a beállítást, folytassa a szimulált eszköz létrehozásának rövid útmutatójával.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)
