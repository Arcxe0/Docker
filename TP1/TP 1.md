# TP 1 - Docker - 27/10/2025

Question 1-1 : The -e is use for security. It allows to give dynamiquly the execution secrets

Question 1-2 : To persist data even after deleting the container. Otherwise, the database is reset each time the system is restarted.

Question 1-3 : 
- Build : docker build -t TP-1 ./database
- Run DB : docker run -d --name postgres-db --network TP-1 -v pgdata:/var/lib/postgresql/data my-database
- Run adminer : docker run -d -p 8090:8080 --network TP-1 --name adminer adminer

Question 1-4 : A multi-stage build allows you to:

- Separate build and execution

- Keep the final image lighter (JRE only, no JDK)

- Avoid the need for local compilation

- Better comply with DevOps best practices (separation of responsibilities)