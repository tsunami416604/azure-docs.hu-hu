### <a name="prerequisites"></a>Előfeltételek
* Twitter-fiók 

Twitter-fiókja, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Twitter-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás szeretne csatlakozni a Twitter-fiókja hitelesítéséhez lépései a következők:

1. A logic app Designerben, Twitter-kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Twitter* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Twitter-kapcsolat lemezkép 0](./media/connectors-create-api-twitter/twitter-0.png)
2. Ha még nem hozott létre a Twitteren előtt kapcsolatokat, első kéri, adja meg a Twitter szolgáltatásbeli hitelesítő adatait. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használandó, és a Twitter-fiókja adatainak eléréséhez:  
   ![Twitter-kapcsolat 1. kép](./media/connectors-create-api-twitter/twitter-1.png)  
3. Adja meg a Twitter-felhasználónév és jelszó a logikai alkalmazás engedélyezése:  
   ![Twitter-kapcsolat lemezkép 2](./media/connectors-create-api-twitter/twitter-2.png)  
4. Az Engedélyezés megerősítése:  
   ![Twitter-kapcsolat lemezkép 3](./media/connectors-create-api-twitter/twitter-3.png)  
5. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Twitter-kapcsolat lemezkép 4](./media/connectors-create-api-twitter/twitter-4.png)

