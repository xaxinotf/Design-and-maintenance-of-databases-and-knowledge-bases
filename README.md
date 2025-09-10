Lab-1-ER-- Я змоделював студії з назвою й адресою <iframe width="560" height="315" src='https://dbdiagram.io/e/67935a4e263d6cf9a0f1fe53/68bb03be61a46d388eaa5785'> </iframe>


<img width="1046" height="579" alt="image" src="https://github.com/user-attachments/assets/98708a9b-7b0f-41db-af9c-85e0dea996df" />
SELECT v.name AS venue, e.stage_name, e.title, e.start_datetime, e.end_datetime
FROM event e
JOIN venue v ON v.id = e.venue_id
WHERE e.stage_name = 'Main Stage'
ORDER BY e.start_datetime;


<img width="1097" height="468" alt="image" src="https://github.com/user-attachments/assets/c8a47085-ea5c-4430-a944-854d09809de1" />
Афіша події з порядком виступів
SELECT e.title, e.start_datetime, a.display_name, p.billing_order, p.role
FROM performance p
JOIN event e  ON e.id = p.event_id
JOIN artist a ON a.id = p.artist_id
WHERE e.title = 'Гала-відкриття'
ORDER BY p.billing_order;





<img width="848" height="761" alt="image" src="https://github.com/user-attachments/assets/f471174c-c87c-4974-a8fb-8e0e20bff55a" />

-- скільки заробив кожен артист за фестиваль
SELECT a.display_name, SUM(c.fee_amount) AS total_fee, MIN(c.currency) AS currency
FROM contract c
JOIN artist a ON a.id = c.artist_id
GROUP BY a.display_name
ORDER BY total_fee DESC;




<img width="858" height="866" alt="image" src="https://github.com/user-attachments/assets/c9261f28-689c-4a99-9768-892f8d7fd423" />
-- Бюджет подій (гонорари + провайдери)
WITH fees AS (
  SELECT e.id, SUM(c.fee_amount) AS fee_total
  FROM event e LEFT JOIN contract c ON c.event_id = e.id
  GROUP BY e.id
),
providers AS (
  SELECT e.id, COALESCE(SUM(ep.fee_amount),0) AS provider_total
  FROM event e LEFT JOIN event_provider ep ON ep.event_id = e.id
  GROUP BY e.id
)
SELECT e.title, e.start_datetime,
       COALESCE(f.fee_total,0)       AS fees,
       p.provider_total              AS providers,
       COALESCE(f.fee_total,0)+p.provider_total AS event_total
FROM event e
LEFT JOIN fees f ON f.id = e.id
LEFT JOIN providers p ON p.id = e.id
ORDER BY e.start_datetime;



<img width="1084" height="919" alt="image" src="https://github.com/user-attachments/assets/6d1054ac-889e-4619-9bf6-b93cf8fb1fb2" />
--Хто виступає на яких сценах і коли (зручний список)
SELECT v.name AS venue, e.stage_name, e.title, a.display_name, p.role, p.duration_minutes
FROM performance p
JOIN event e  ON e.id = p.event_id
JOIN venue v  ON v.id = e.venue_id
JOIN artist a ON a.id = p.artist_id
ORDER BY v.name, e.stage_name, e.start_datetime, p.billing_order;




<img width="840" height="552" alt="image" src="https://github.com/user-attachments/assets/8fbb6ffd-44ff-45ee-b16b-6721ac34d538" />
-- виступи без контракту (має бути 0 рядків)
SELECT e.title, a.display_name
FROM performance p
JOIN event e  ON e.id = p.event_id
JOIN artist a ON a.id = p.artist_id
LEFT JOIN contract c ON c.event_id = p.event_id AND c.artist_id = p.artist_id
WHERE c.artist_id IS NULL;








<img width="1535" height="1261" alt="image" src="https://github.com/user-attachments/assets/e787f7c0-c53e-4edb-b48e-9189e2928054" />

