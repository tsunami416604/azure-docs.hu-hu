## <a name="updating-properties-on-an-existing-cloud-spatial-anchor"></a>A meglévő térbeli felhőbeli horgony tulajdonságainak frissítése

A horgony tulajdonságainak frissítéséhez a UpdateAnchorPropertiesAsync módszert használja. Ha két vagy több eszközt próbálja frissíteni ugyanazt a forráshorgony tulajdonságait egyszerre, egy optimista párhuzamossági modellt használjuk. Ami azt jelenti, hogy az első írási legyőzi-e.  Minden más írást egy "Egyidejűségi" hiba jelenik: tulajdonság frissítését lenne szükséges, mielőtt újra próbálkozna.
