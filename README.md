# SQLi-CTF

A training CTF covering non-blind SQL injection techniques

## Requirements

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)

## Deploying

	git clone https://github.com/CFI-UL/SQLi-CTF
	cd SQLi-CTF
	git submodule update
	docker-compose up

The CTFd and challenges will be available at http://127.0.0.1:8000

Now reverse proxy it behind a letsencrypt enabled nginx/apache (this is left as an exercise to the reader)

Default creds are `admin:admin`, don't forget them
