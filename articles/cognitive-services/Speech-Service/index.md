---
layout: HubPage
ms.date: 01-24-2020
ms.topic: hub-page
title: A Speech szolgáltatás dokumentációja
description: Bevezetés a Speech Service használatába. Beszédfelismerés, beszédszintézis, valós idejű fordítások, beszédátírás vagy beszéd integrálása a robotokba.
ms.openlocfilehash: a965090be737a10633ead2ca1ed7033ec061ed7f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771981"
---
<div id="main" class="v2">
    <div class="container">
        <h1>A Speech szolgáltatás dokumentációja</h1>
        <ul class="pivots">
            <li>
                <a href="#start">Első lépések</a>
                <ul id="start">
                    <li>
                        <a href="#speechtotext">Beszédfelismerés</a>
                        <ul id="speechtotext" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/sr-microphone">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-recognition-from-microphone-file.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszéd felismerése mikrofonnal</h3>
                                                    <p>A Speech SDK használatával felismerheti a mikrofonból érkező beszédet, és szöveggé alakíthatja a kimenetet.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sr-file">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-recognition-from-file-file.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszéd felismerése hangfájlból</h3>
                                                    <p>A Speech SDK használatával felismerheti a beszédet egyetlen fájlból, és szöveggé alakíthatja a kimenetet.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sr-blob">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-recognition-from-blob.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Aszinkron felismerés blobokból</h3>
                                                    <p>A REST szolgáltatással aszinkron módon ismerheti fel a beszédet az Azure Blob Storage-ban tárolt fájlokból.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sr-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>További információ a beszédfelismerés programozásáról és beszélt nyelvi támogatásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sr-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a beszédfelismeréshez kapcsolódó költségekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sr-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>Megtudhatja, hogyan építhet be beszédfelismerést az alkalmazásaiba, eszközeibe és termékeibe. Tartalmazza a fogalmakat, oktatóanyagokat, API-referenciát és kiadási megjegyzéseket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                        <li>
                            <a href="#texttospeech">Szövegfelolvasás</a>
                            <ul id="texttospeech" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/tts-speaker">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-synthesis-to-audio-out.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszédszintézis hangszóróra</h3>
                                                    <p>A Speech SDK használatával hangkimenetre, például hangszóróra szintetizálhatja a beszédet.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/tts-file">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-synthesis-to-file.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszédszintézis hangfájlba</h3>
                                                    <p>A Speech SDK használatával hangfájlba szintetizálhatja a beszédet.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>                            
                            <li>
                                <a href="https://aka.ms/speech/tts-ssml">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszédszintézis-jelölőnyelv</h3>
                                                    <p>A beszédszintézis-jelölőnyelv használatával finomhangolhatja a hangkimenet hangmagasságát, hanglejtését és beszédgyorsaságát.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/tts-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>Megtudhatja, hogy a beszédszintézis mely nyelveket támogatja.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/tts-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a szövegfelolvasáshoz kapcsolódó költségekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/tts-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>Megtudhatja, hogyan építhet be beszédszintézist az alkalmazásaiba, eszközeibe és termékeibe. Tartalmazza a fogalmakat, oktatóanyagokat, API-referenciát és kiadási megjegyzéseket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#intentrecognition">Szándékfelismerés</a>
                        <ul id="intentrecognition" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/intent-microphone">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-intent-recognition.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszéd, szándékok és entitások felismerése</h3>
                                                    <p>A Speech SDK és a Language Understanding (LUIS) használatával felismerheti a beszédet, a szándékokat és az entitásokat.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/intent-luis-docs">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A Language Understanding (LUIS) dokumentációja</h3>
                                                    <p>További információ a Language Understanding szolgáltatásról (LUIS) és a természetes nyelvi feldolgozásról (Natural Language Processing, NLP).</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://www.luis.ai/">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A Language Understanding (LUIS) portálja</h3>
                                                    <p>Természetes nyelvet építhet be az alkalmazásokba, robotokba és IoT-eszközökbe.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-translation">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>Megtudhatja, hogy a szándékfelismerés mely nyelveket támogatja.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a szándékfelismeréshez kapcsolódó költségekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>Megtudhatja, hogyan építhet be szándékfelismerést az alkalmazásaiba, eszközeibe és termékeibe. Tartalmazza a fogalmakat, oktatóanyagokat, API-referenciát és kiadási megjegyzéseket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#speechtranslation">Beszédfordítás</a>
                        <ul id="speechtranslation" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/sttt-microphone">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-translation-from-file.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Mikrofonból felismert beszéd fordítása</h3>
                                                    <p>A Speech SDK használatával lefordíthatja a mikrofonból felismert beszédet.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sttt-multiple-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-translation-from-microphone-file.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszéd fordítása több célnyelvre</h3>
                                                    <p>A Speech SDK használatával lefordíthatja a beszédet több célnyelvi kimenetre.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/stst-microphone">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-translation-from-microphone-audio-out.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszéd fordítása beszédre</h3>
                                                    <p>A Speech SDK használatával lefordíthatja a beszédet beszédre.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sttt-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>További információ a beszédfordítás programozásáról és beszélt nyelvi támogatásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sttt-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a beszédfordításhoz kapcsolódó költségekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/sttt-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>Megtudhatja, hogyan építhet be tolmácsolást az alkalmazásaiba, eszközeibe és termékeibe. Tartalmazza a fogalmakat, oktatóanyagokat, API-referenciát és kiadási megjegyzéseket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#conversationtranscription">Beszédátírás</a>
                        <ul id="conversationtranscription" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/cts-microphone">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-conversation-transcription.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Valós idejű beszélgetés átírása</h3>
                                                    <p>Tudnivalók a beszélgetések valós időben történő átírásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/cts-async-op">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-conversation-transcription.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Beszélgetések aszinkron átírása</h3>
                                                    <p>Megtudhatja, hogyan írhat át beszélgetéseket aszinkron módon, kérdezheti le az állapotot, és töltheti le a kimeneteket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/call-center-transcription">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-call-center-transcription.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Telefonos ügyfélszolgálati felvételek átírása</h3>
                                                    <p>Megtudhatja, hogyan írhat át nagy mennyiségű, különböző rendszerekről származó telefonos adatot.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/cts-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>További információ a beszédátírás programozásáról és beszélt nyelvi támogatásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/cts-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információk a beszédátíráshoz kapcsolódó költségekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/cts-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>További információ a beszédátírásról és a termékekbe való integrálásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#voice-assistants">Hangvezérelt asszisztensek</a>
                        <ul id="voice-assistants" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/va-bot-framework">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-conversational-agent.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Integrálás a Bot Frameworkkel</h3>
                                                    <p>További információ a Bot Frameworkről, a Direct Line Speech-csatornáról és egyebekről.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/va-no-code">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Egyéni parancsok használata</h3>
                                                    <p>Megtudhatja, hogyan hozhat létre egyszerűen hatékony hangalapú parancs- és vezérlési alkalmazásokat, amelyekkel a felhasználók saját hangjukat használva hajthatnak végre feladatokat.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/va-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-reference.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>A dokumentáció elolvasása</h3>
                                                    <p>További információ a hangvezérelt asszisztensek létrehozásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#deploy-architecture">Üzemeltetés</a>
                        <ul id="deploy-architecture" class="cardsC">
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Azure Speech-szolgáltatáserőforrás létrehozása</h3>
                                                    <p>Megtudhatja, hogyan kaphat előfizetési kulcsot, hogyan hozhat létre Speech-erőforrást és hogyan végezheti el az első lépéseket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="speech-container-howto.md?tabs=stt">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-containers.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Speech-szolgáltatástárolók üzembe helyezése</h3>
                                                    <p>Megtudhatja, hogyan használhatja a Speech-tárolókat a helyszínen, a Kubernetesszel, a Helmmel vagy az Azure Container Instanceszel.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/sovereign-clouds">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-regions.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Független felhők</h3>
                                                    <p>A Speech-szolgáltatáserőforrások rendelkezésre állása különálló Azure-felhőkben.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="regions.md">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-regions.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Támogatott régiók</h3>
                                                    <p>Megtudhatja, hol támogatott a Speech szolgáltatás.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#support">Támogatás</a>
                        <ul id="support" class="cardsC">
                            <li>
                                <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-github-issues.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>GitHub-problémák</h3>
                                                    <p>Böngészhet a nyitott problémák között és/vagy új problémákat hozhat létre a Speech SDK-hoz a GitHubon.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-stackoverflow.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Stack Overflow</h3>
                                                    <p>Felteheti a kérdéseit, és segítséget kérhet a Speech szolgáltatást használó közösségtől a Stack Overflow fórumán.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://cognitive.uservoice.com/forums/912208-speech-service">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-uservoice.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>UserVoice-fórum</h3>
                                                    <p>Megoszthatja ötleteit, fejlesztéseket javasolhat vagy új funkciókat kérhet a Speech szolgáltatáshoz.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/scenario-availability">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Forgatókönyvek és funkciók elérhetősége</h3>
                                                    <p>Megismerheti a Speech SDK funkcióinak számos felhasználási körét különféle programozási nyelveken és környezetekben.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/regions">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-regions.png" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Támogatott régiók</h3>
                                                    <p>Megismerheti a Speech szolgáltatás rendelkezésre álló régióit és végpontjait.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                </ul>
            </li>
            <li>
                <a href="#customization">Testreszabás</a>
                <ul id="customization">
                    <li>
                        <a href="#speech-to-text-customization">Beszédfelismerés</a>
                        <ul id="speech-to-text-customization" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/phrase-list">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Pontosság javítása kifejezéslistákkal</h3>
                                                    <p>A kifejezéslisták Speech SDK-ban történő használatával javíthatja a felismerés pontosságát.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/tlm-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Pontosság javítása bérlőmodellekkel</h3>
                                                    <p>Az Office 365-adatokkal létrehozhat egyéni modelleket, amelyekkel a cégére jellemző kifejezésekre optimalizálhatja a beszédfelismerés pontosságát.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-sr-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Pontosság javítása Custom Speech segítségével</h3>
                                                    <p>Olyan online eszközök készlete, amelyek lehetővé teszik a Microsoft beszédfelismerési pontosságának értékelését és javítását alkalmazásai, eszközei és termékei számára.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-sr-languages">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>További információ a Custom Speech programozásáról és beszélt nyelvi támogatásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-sr-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a Custom Speechhez kapcsolódó költségekről.
                                                    </p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li>
                        <a href="#text-to-speech-customization">Szövegfelolvasás</a>
                        <ul id="text-to-speech-customization" class="cardsC">
                            <li>
                                <a href="https://aka.ms/speech/tts-ssml">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Szintézis javítása az SSML-lel</h3>
                                                    <p>A beszédszintézis-jelölőnyelv használatával finomhangolhatja a hangkimenet hangmagasságát, hanglejtését és beszédgyorsaságát.
                                                    </p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-tts-overview">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Szintézis javítása a Custom Voice-szal</h3>
                                                    <p>Létrehozhat egy felismerhető és egyedülálló hangot szövegfelolvasási alkalmazásaihoz az elérhető beszédadatokkal.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-tts-audio-content-creation">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-how-to.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Szintézis javítása az Audio Content Creationnel</h3>
                                                    <p>Az Audio Content Creation eszközzel finomhangolhatja a szintetizált hangkimeneteket.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-tts-neural">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-human-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Hozzáférés a Custom Neural Voiceshoz</h3>
                                                    <p>Ez egy korlátozott funkció, amely lehetővé teszi egy egyéni neurális hang létrehozását. További információ a hozzáférésről és a korlátozásokról.
                                                    </p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-generic-programming-languages.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Nyelvi támogatás</h3>
                                                    <p>További információ a Custom Voice programozásáról és beszélt nyelvi támogatásáról.</p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                            <li>
                                <a href="https://aka.ms/speech/custom-tts-pricing">
                                    <div class="cardSize">
                                        <div class="cardPadding">
                                            <div class="card">
                                                <div class="cardImageOuter">
                                                    <div class="cardImage bgdAccent1">
                                                        <img src="media/hub/speech-pricing.jpg" alt="" />
                                                    </div>
                                                </div>
                                                <div class="cardText">
                                                    <h3>Díjszabás</h3>
                                                    <p>További információ a Custom Voice-hoz kapcsolódó költségekről.
                                                    </p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </li>
                        </ul>
                    </li>
                </ul>
            </li>
        </ul>
    </div>
</div>
