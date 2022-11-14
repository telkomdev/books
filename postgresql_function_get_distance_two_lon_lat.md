### Postgresql | Function Get Distance between two Longitude and Latitude

```sql
CREATE OR REPLACE FUNCTION get_distance(lon1 FLOAT, lat1 FLOAT, lon2 FLOAT, lat2 FLOAT) 
    RETURNS FLOAT
    LANGUAGE plpgsql
AS 
$$
DECLARE                                                   
    one_mile FLOAT = 1.609344; -- 1 Mile = 1.609344 KM
    distance_in_mile FLOAT = (point(lon1,lat1) <@> point(lon2,lat2));      
BEGIN                                                     
    RETURN one_mile * distance_in_mile;                            
END;  
$$;
```
