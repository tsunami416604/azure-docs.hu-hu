---
title: Azure Security Center oktatóanyag – Biztonsági szabályzatok meghatározása és értékelése | Microsoft Docs
description: Azure Security Center oktatóanyag – Biztonsági szabályzatok meghatározása és értékelése
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: a740cec8a3177e0de5f33b362d5c4956671cd427
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-define-and-assess-security-policies"></a>Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése
A Security Center a számítási feladatokhoz tartozó kívánt konfigurációk biztonsági szabályzatokkal történő meghatározásával segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Az Azure-előfizetésekre vonatkozó szabályzatok meghatározása és azoknak a számítási feladat típusához vagy az adatok érzékenységéhez történő igazítása után a Security Center biztonsági javaslatokat kínál a számítási, hálózati, SQL- és tárolási, illetve az alkalmazásokra vonatkozó erőforrásokhoz. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Biztonsági szabályzat konfigurálása
> * Az erőforrások biztonságának felmérése

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="configure-security-policy"></a>Biztonsági szabályzat konfigurálása
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A biztonsági szabályzatok olyan javaslatokat tartalmaznak, amelyek az adott előfizetéshez tartozó biztonsági követelményeknek megfelelően be- és kikapcsolhatók. Az alapértelmezett biztonsági szabályzat módosításához az előfizetésben tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie.

1. A Security Center főmenüjében válassza a **Biztonsági szabályzat** elemet. Válassza ki a használni kívánt előfizetést. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Biztonsági szabályzat** lehetőséget:

  ![Biztonsági szabályzat](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

2. Minden egyes monitorozni kívánt biztonsági konfiguráció esetében válassza a **Be** lehetőséget. A Security Center folyamatosan értékelni fogja a környezet konfigurációját, és ha biztonsági rést fedez fel, biztonsági javaslatot hoz létre. Ha az adott biztonsági konfiguráció nem javasolt vagy nem érvényes, válassza a **Ki** lehetőséget. Például egy fejlesztői vagy tesztkörnyezetben általában más biztonsági szintek szükségesek, mint egy éles környezetben. Az adott környezetnek megfelelő szabályzatok kiválasztása után kattintson a **Mentés** elemre.

Várja meg, amíg a Security Center feldolgozza a szabályzatokat, és létrehozza a javaslatokat. Néhány konfiguráció, például a rendszerfrissítések vagy az operációs rendszer beállítsa akár 12 órát is igénybe vehet, miközben a hálózati biztonsági csoportok és a titkosítási konfigurációk kiértékelése szinte azonnal megtörténik. Ha a javaslatok megjelennek a Security Center irányítópultján, folytassa a következő lépéssel.

## <a name="assess-security-of-resources"></a>Biztonsági erőforrások felmérése
1. Az engedélyezett biztonsági szabályzatnak megfelelően a Security Center elkészíti a szükséges biztonsági javaslatokat. Érdemes a virtuális gépekre és számítógépekre vonatkozó javaslatokkal kezdenie. A Security Center irányítópultján kattintson az **Áttekintés**, majd a **Számítás** elemre.

  ![Számítás](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Tekintse át az egyes javaslatokat a pirossal kiemeltekkel kezdve (ezek a legmagasabb proiritásúak). A javaslatok némelyike a Security Centerből közvetlenül alkalmazható szervizelési megoldást tartalmaz, amilyen például [a végpontvédelem kérdése](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Más javaslatok csak útmutatást fogalmaznak meg a szervizeléshez, például a hiányzó lemeztitkosításra vonatkozóan.

2. Ha a számításra vonatkozó összes javaslatot sikerült feldolgozni, továbbléphet a következő számítási feladatra, vagyis a hálózatra. A Security Center irányítópultján kattintson az **Áttekintés**, majd a **Hálózat** elemre.

  ![Hálózat](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  A hálózatokra vonatkozó javaslatok oldala a hálózati beállításhoz, az internet felé néző végpontokhoz és a hálózati topológiához kapcsolódó biztonsági problémák listáját tartalmazza. Ahogyan a **Számítás** esetében, a hálózatokra vonatkozó javaslatoknál is egyes ajánlások beépített szervizelést kínálnak, mások pedig nem.

3. Ha a hálózatokra vonatkozó összes javaslatot sikerült feldolgozni, továbbléphet a következő számítási feladatra, vagyis a tárolás és adatok részhez. A Security Center irányítópultján kattintson az **Áttekintés**, majd a **Tárolás és adatok** elemre.

  ![Adatforrások](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  Az **Adatforrások** oldal az Azure SQL Server és adatbázisok naplózásának engedélyezésével, az SQL-adatbázisok titkosításának engedélyezésével, illetve a saját Azure-tárfiók titkosításának engedélyezésével kapcsolatos javaslatokat tartalmaz. Ha nincs ilyen számítási feladat, nem jelenik meg javaslat. Ahogyan a **Számítás** esetében, az SQL-re és tárolásra vonatkozó javaslatoknál is egyes ajánlások beépített szervizelést kínálnak, mások pedig nem.

4. Ha az SQL-re és a tárolásra vonatkozó összes javaslatot sikerült feldolgozni, továbbléphet a következő számítási feladatra, vagyis az alkalmazásokra. A Security Center irányítópultján kattintson az **Áttekintés**, majd a **Alkalmazások** elemre.

  ![Alkalmazások](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  Az **Alkalmazások** oldal a webalkalmazási tűzfalak telepítésére, illetve az alkalmazások általános korlátozására vonatkozó javaslatokat tartalmaz. Ha nem rendelkezik virtuális géppel vagy az Internet Information Service (IIS) használatával futtatott webalkalmazásokat tartalmazó számítógéppel, ezek a javaslatok nem jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban arról volt szó, hogyan végezhető el az alapszintű szabályzatok meghatározása és a számítási feladatok biztonsági értékelése a Security Center használatával, például:

> [!div class="checklist"]
> * A biztonsági szabályzat konfigurálása a vállalati vagy hatósági követelményeknek való megfelelés biztosítása érdekében
> * A biztonsági felmérés készítése a számítási, hálózati, SQL- és tárolási, illetve az alkalmazásokra vonatkozó erőforrásokhoz

A következő oktatóanyag azt mutatja be, hogyan alkalmazható a Security Center az erőforrások védelmére.

> [!div class="nextstepaction"]
> [Az erőforrások védelme](tutorial-protect-resources.md)
