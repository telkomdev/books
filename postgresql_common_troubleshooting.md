## Postgresql Common Troubleshooting

### Error `PostgreSQL Error: cannot execute in a read-only transaction`
Reason: PostgreSQL server is recovery(read-only) or standby mode.
Solution: We have to remove recovery (read-only or standby mode file, usually located on `/var/lib/postgresql/<version>/main/<standby | recovery>.signal`

### Cannot connect to DB server
Reason: usually caused by the postgresql session is full, and can not serve new connections
Solution: by increasing `max_connection` & `shared_buffers` on PostgreSQL configuration will solve the problem, ex: `max_connections=150`, `shared_buffers=256MB`
