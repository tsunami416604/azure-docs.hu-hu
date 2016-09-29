<properties
    pageTitle="Csomag létrehozása az Azure Stackben | Microsoft Azure"
    description="Szolgáltatás-rendszergazdaként, hozzon létre egy csomagot, amely lehetővé teszi az előfizetők számára a virtuális gépek létrehozását."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="erikje"/>


# Csomag létrehozása az Azure Stackben

A [Csomagok](azure-stack-key-features.md#services-plans-offers-and-subscriptions) egy vagy több szolgáltatás csoportjai. Szolgáltatóként csomagokat hozhat létre, amelyeket kiajánlhat a bérlőinek. A bérlői előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat. Ebben a példában egy csomagot fog létrehozni, amely tartalmazza a számítási, hálózati és tárolási erőforrás-szolgáltatókat. Ez lehetőséget biztosít a csomag előfizetőinek a virtuális gépek létrehozására.

1.  Egy böngészőből keresse fel a https://portal.azurestack.local. címet.

2.  [Jelentkezzen be](azure-stack-connect-azure-stack.md#log-in-as-a-service-administrator) az Azure Stack Portalra szolgáltatás-rendszergazdaként, és írja be szolgáltatás-rendszergazdai hitelesítő adatait (ez az a fiók, amelyet [A PowerShell-parancsprogram futtatása](azure-stack-run-powershell-script.md) 5. lépésében létrehozott), majd kattintson a **Bejelentkezés** parancsra.

    A szolgáltatás-rendszergazdák létrehozhatnak ajánlatokat és csomagokat, valamint kezelhetik a felhasználókat.

3.  Egy új csomag vagy ajánlat létrehozásához (amelyre a bérlők előfizethetnek) kattintson az **Új** elemre.

    ![](media/azure-stack-create-plan/image1.png)

4.  A Létrehozás panelen kattintson a **Bérlői ajánlatok és csomagok**, majd a **Csomag** elemre.

    ![](media/azure-stack-create-plan/image2.png)

5.  Töltse ki a **Megjelenítendő név** és **Erőforrás neve** mezőt. A megjelenítendő név a csomag rövid neve. Kizárólag a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák a csomaggal mint Azure Resource Manager-erőforrással végzett műveletekhez.

    ![](media/azure-stack-create-plan/image3.png)

6.  Válasszon ki vagy hozzon létre egy új **erőforráscsoportot** a csomag tárolójaként. Alapesetben az összes csomag és ajánlat az OffersAndPlans nevű erőforráscsoportba fog kerülni.

7.  Kattintson az **Ajánlott szolgáltatások** elemre, használja a Shift billentyűt mind a három szolgáltató csoportos kijelöléséhez (**számítási szolgáltató**, **társzolgáltató** és **hálózati szolgáltató**), majd kattintson a **Kiválasztás** elemre.

    ![](media/azure-stack-create-plan/image4.png)

8.  Kattintson a **Microsoft.Compute**, majd a **Konfigurálás szükséges** elemre.

    ![](media/azure-stack-create-plan/image5.png)

9.  A **Kvóták beállítása** panelen fogadja el az összes alapértelmezett beállítást, és kattintson az **OK**, majd ismét az **OK** gombra.

    ![](media/azure-stack-create-plan/image6.png)

10. Kattintson a **Microsoft.Network**, majd a **Konfigurálás szükséges** elemre.

    ![](media/azure-stack-create-plan/image7.png)

11. A **Kvóták beállítása** panelen jelölje be az összes jelölőnégyzetet, és kattintson az **OK**, majd ismét az **OK** gombra.

    ![](media/azure-stack-create-plan/image8.png)

12. Kattintson a **Microsoft.Storage**, majd a **Konfigurálás szükséges** elemre, és a **Kvóták beállítása** panelen fogadja el az összes alapértelmezett beállítást, kattintson az **OK**, majd ismét az **OK** gombra, végül válassza a **Létrehozás** parancsot a csomag létrehozásához.

    ![](media/azure-stack-create-plan/image9.png)

13. A csomagja mostantól szerepeltethető egy ajánlatban. Az értesítések megtekintéséhez kattintson a jobb felső részen található csengőre.

    ![](media/azure-stack-create-plan/image10.png)

## Következő lépések

[Ajánlat létrehozása](azure-stack-create-offer.md)



<!--HONumber=Sep16_HO3-->


