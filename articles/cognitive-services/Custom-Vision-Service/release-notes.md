---
title: Kibocsátási megjegyzések - Egyéni vision szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Custom Vision csapatlegújabb információit az új kiadásokról.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647570"
---
# <a name="custom-vision-service-release-notes"></a>Egyéni vision szolgáltatás kibocsátási megjegyzések

## <a name="may-2-2019-and-may-10-2019"></a>2019. május 2., 2019.

- Hibajavítások és háttérfejlesztések

## <a name="may-23-2019"></a>2019. május 23.May 23, 2019

- Az Azure-előfizetésekhez kapcsolódó továbbfejlesztett portálfelhasználói élmény, amely megkönnyíti az Azure-könyvtárak kiválasztását.

## <a name="april-18-2019"></a>2019. április 18.April 18, 2019 

- Objektumészlelés exportálása hozzáadva a Vision AI fejlesztői készlethez.
- A felhasználói felület csípése, beleértve a projektkeresést is.

## <a name="april-3-2019"></a>2019. április 3.April 3, 2019

- Képenként a határolódobozok számának 200-ra növelve a korlátot. 
- Hibajavítások, beleértve a TensorFlow-ba exportált modellek jelentős teljesítményfrissítését. 

## <a name="march-26-2019"></a>2019. március 26.March 26, 2019

- A Custom Vision Service általános elérhetőséget adott meg az Azure-ban!
- Speciális oktatás funkció egy új gépi tanulási háttérrendszerrel a jobb teljesítmény érdekében, különösen a kihívást jelentő adatkészletek és a részletes besorolás esetén. A speciális képzéssel megadhatja a betanítási idő számítási időkeretét, és a Custom Vision kísérletileg azonosítja a legjobb képzési és bővítési beállításokat. A gyors ismétlések, továbbra is használhatja a meglévő gyors képzés.
- Bevezetett 3.0 API-k. 2019. október 1-jén bejelentette a 3.0 előtti API-k eközben történő e-mentesítését. A [.Net,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial) [Python,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial) [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)vagy [Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) dokumentációs rövid útmutatóiban példákat talál az első lépésekhez.
- Az "Alapértelmezett ismétlések" helyébe a 3.0 API-k közzététele/visszavonása.
- Új modell exportcélok kerültek hozzáadásra. A Dockerfile exportálása frissítve lett a Raspberry Pi 3 ARM támogatásához. Az exporttámogatás hozzáadva lett a [Vision AI Fejlesztői készlethez.](https://visionaidevkit.com/)
- A címkék projektenkénti korlátjának megnövelt száma 500-ra nőtt az S0 szint esetében. Projektenként a képek száma 100 000-re nőtt az S0 szint esetében.
- Eltávolítva felnőtt tartomány. Ehelyett az általános tartomány használata ajánlott.
- Általános [elérhetőségi díjszabás.](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)  

## <a name="february-25-2019"></a>2019. február 25.February 25, 2019

- Bejelentette a korlátozott próbaprojektek (az Azure-erőforrásokhoz nem kapcsolódó projektek) befejezését, mivel a Custom Vision az Azure nyilvános előzetes verzióra való áthelyezésének befejezéséhez közeledik. 2019. március 25-től a CustomVision.ai webhely csak egy Azure-erőforráshoz kapcsolódó projektek, például az ingyenes Custom Vision-erőforrás megtekintését támogatja. 2019. október 1-ig a Custom Vision API-kon keresztül továbbra is elérheti a meglévő korlátozott próbaprojekteket. Így időt adhat az API-kulcsok frissítésére a Custom Vision alkalmazással írt alkalmazásokhoz. 2019. október 1-je után minden olyan korlátozott próbaprojekt törlődik, amelyet nem helyezett át az Azure-ba.

## <a name="january-22-2019"></a>2019. január 22.

- Az új Azure-régiók támogatása: USA nyugati régiói 2, USA keleti régiója, USA keleti régiója 2, Nyugat-Európa, Észak-Európa, Délkelet-Ázsia, Kelet-Ausztrália, Közép-India, Az Egyesült Királyság déli része, Kelet-Japán és USA északi középső régiója. Továbbra is támogatják az USA déli középső központját.

## <a name="december-12-2018"></a>2018. december 12.December 12.December 12.December 12, 2018

- Objektumészlelési modellek exportálásának támogatása (bevezetett objektumészlelési kompakt tartomány).
- Számos kisegítő lehetőség gel kapcsolatos probléma a képernyőolvasó és a billentyűzet navigációjának javítása érdekében.
- A felhasználói felület frissítései a képnézegetőhöz és a továbbfejlesztett objektumészlelési címkézési élmény a gyorsabb címkézés érdekében.  
- Az objektumészlelési tartomány frissített alapmodellje a jobb minőségű objektumészlelés érdekében.
- Hibajavítások.

## <a name="november-6-2018"></a>2018. november 6.,

- Az Objektumészlelésben az Emblématartomány támogatása hozzáadva.

## <a name="october-9-2018"></a>2018. október 9.

- Az objektumészlelés a fizetős előnézetbe kerül. Most már létrehozhat objektumészlelési projekteket egy Azure-erőforrással.
- Az "Áthelyezés az Azure-ba" funkcióval bővült a webhely, így könnyebben frissíthető egy korlátozott próbaverziós projekt, amely egy Azure-ra mutató hivatkozást tesz le. erőforráshoz kötött projekt (F0 vagy S0.) Ezt a termék beállítások oldalán találja.  
- Az ONNX 1.2-es verziójának exportálása a Windows ML októberi windows update verziójának támogatásához.
Hibajavítások, beleértve az ONNX exportspeciális karakterekkel.

## <a name="august-14-2018"></a>2018. augusztus 14.August 14, 2018

- Hozzáadott "Get Started" widget customvision.ai webhelyet, hogy végigvezesse a felhasználókat a projekt képzésén.
- További fejlesztések a gépi tanulási folyamat javára többcímkés projektek (új veszteségréteg).

## <a name="june-28-2018"></a>2018. június 28.June 28, 2018

- Hibajavítások & háttérfejlesztések.
- Engedélyezett többosztályos besorolás, olyan projekteknél, ahol a képek pontosan egy címkével rendelkeznek. A többosztályos módban megadott előrejelzések ben a valószínűségek egy rekednek (az összes kép a megadott címkék közé van besorolva).

## <a name="june-13-2018"></a>2018. június 13.

- Felhasználói felület frissítése, amely a könnyű használatra és a kisegítő lehetőségekre összpontosít.
- Fejlesztések a gépi tanulási folyamat javára többcímkés projektek nagy számú címkéket.
- Javítva a TensorFlow exporthibája. Engedélyezett exportált modell verziószámozás, így az ismétlések többször is exportálhatók.

## <a name="may-7-2018"></a>2018. május 7.

- Előzetes verziójú objektumészlelési szolgáltatás bevezetése korlátozott próbaverziójú projektekhez.
- Frissítés 2.0 API-kra
- Az S0 szint kibővítve akár 250 címkére és 50 000 képre.
- Jelentős háttérbeli fejlesztések a képosztályozási projektek gépi tanulási folyamataiban. A 2018. április 27. után betanított projektek már kihasználják ezen frissítések előnyeit.
- Hozzáadott modellexportálás az ONNX-hez, Windows ML-el való használathoz.
- Hozzáadott modellexportálás a Dockerfile-hoz. Ez lehetővé teszi az összetevők, például a DockerFile, a TensorFlow-modell és a szolgáltatáskód letöltését saját Windows- vagy Linux-tárolók létrehozásához.
- Az általános (kompakt) és landmark (kompakt) tartományokban a TensorFlow-ba exportált újonnan betanított modellek esetében az [átlagértékek most (0,0,0) az](https://github.com/azure-samples/cognitive-services-android-customvision-sample)összes projekt konzisztenciája érdekében.

## <a name="march-1-2018"></a>2018. március 1.

- Beírt fizetett előzetes és -ra -ra -ra a Azure portal. Mostantól csatolhatók projektek az F0 (Ingyenes) vagy S0 (Standard) szintű Azure-erőforrásokhoz. S0 szintű projektek bevezetve, amelyek akár 100 címkét és 25 000 képet is engedélyeznek.
- Háttérbeli módosítások a gép tanulási folyamatban/normalizálási paraméterben. Ezzel a felhasználók jobban kezelhetik a pontosság-visszahívási előnyöket és hátrányokat a valószínűségi küszöbérték beállításakor. A módosítások részeként az alapértelmezett valószínűségi küszöbérték a CustomVision.ai portálon 50%-ra lett állítva.

## <a name="december-19-2017"></a>2017. december 19.

- Exportálás Androidra (TensorFlow) hozzáadva a korábban kiadott iOS (CoreML) exportálásmellett. Ez lehetővé teszi, hogy egy betanított kompakt modell exportálása offline módban fusson egy alkalmazásban.
- Kiskereskedelem és Tereptárgy „kompakt” tartományok hozzáadva, így elérhetővé válik bennük a modellexportálás.
- Az [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) és az [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) kiadva. A frissített API-k támogatják a modellexportálást, illetve az új előrejelzési műveletet, amely nem menti a képeket az előrejelzések közé. Bevezetett kötegelt műveletek a Training API-ban.
- UX-finomhangolások, beleértve azt a lehetőséget, amellyel megtekinthető, hogy melyik tartomány használatával lett betanítva az ismétlés.
- Frissült a [C# SDK és minta](https://github.com/Microsoft/Cognitive-CustomVision-Windows).