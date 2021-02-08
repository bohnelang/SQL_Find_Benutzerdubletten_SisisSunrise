# Benutzer-Dupletten in Sybase am Beispiel von Sisis Sunrise finden

### Self-Join kombiniert mit einem Sub-Select

Die Grundidee ist, dass ein Benutzer doppelt vorhanden ist, wenn das Geburtsdatum gleich ist und der Vorname auch in Teilen vorkommt.


```
Beispiel:

Benutzer 1: 15.08.2000 Tobias Klaus Hermann Müller
Benutzer 2: 15.08.2000 Tobias Müller
```

Dieses ist mit großer Sicherheit eine Dublette, die aber so nicht aufgrund des stark unterschiedlichen Vornamens erkannt wird. 


Mit dieser Datenbankabfrage kann man ein Großteil dieser Dubletten finden:


```
SELECT  T1.d02bnr, T2.d02bnr, T1.d02vname, T1.d02name, T1.d02gedatum
  FROM sisis.sisis.d02ben as T1
    INNER  JOIN  sisis.sisis.d02ben AS T2 ON T1.d02gedatum=T2.d02gedatum and T1.d02name=T2.d02name
      WHERE NOT T1.d02bnr=T2.d02bnr AND ( (T1.d02vname LIKE ( SELECT '%' + T2.d02vname + '%') ) OR (T2.d02vname LIKE ( SELECT '%' + T1.d02vname + '%')))
        ORDER BY T1.d02name
  ```
