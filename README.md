# TeamCity Multinode Setup Examples

This repo shows TeamCity multinode Setup examples with different reverse proxies. **For demo purposes only.**

Starts TeamCity in docker-compose with several servers of different roles (Ubuntu-based).

Requires at least 4Gb of RAM in the docker engine.

- Uses Postgres DB (`psql -p5433 -hlocalhost -Utest -dbuildserver`, password `test`).
- Starts two servers nodes, two build agents, and a reverse proxy (NGINX/HAProxy).

For details on volumes, created folders, and logs see the `docker-compose.yml` file.

## To start

1. Start the Docker Compose deployment:

```
cd teamcity-multinode-<proxy-type>
docker-compose up -d
```

2. Initialize teamcity-node1.  
  2.1 Run `docker compose logs teamcity-node1 | grep Super` and Copy the superuser token. If there is no token in the log, wait a minute and rerun the command.  
  2.2. Go to http://localhost:8111 and paste the superuser token if required.  
  2.3. Click **Proceed** to initialize a new data directory, then tick 'run this node as **main**' and click **Proceed** again.  
  2.4. Accept license agreement and create and specify the admin username and password.

3. Initialize teamcity-node2.  
  3.1 Copy the superuser token from node2 logs: `docker compose logs teamcity-node2 | grep Super`    
  2.2. Go to http://localhost:8112 and paste the superuser token.  
  2.3. Click **Proceed** to use the default data directory. While the node is initializing, you can proceed to the next step.

4. Log into TeamCity through the proxy http://localhost:8222 and authorize the connected agents.

Next, open http://localhost:8111 for the initial setup.

After creating a user, visit ["Agents -> Unauthorized"](http://localhost:8111/agents.html?tab=unauthorizedAgents) to authorize the build agent.

See http://localhost:8111/admin/admin.html?item=nodesConfiguration for nodes configuration.

## To shutdown

```
docker-compose down
```


## NGINX

No HTTPS/SSL example for now.

## HAProxy

This example works with HTTPS and SSL termination (but agents still connect via HTTP for now). The certificate is self-signed so there is no automatic redirection from HTTP to HTTPS. If your `haproxy.pem` certificate is valid, you can uncomment the `http-request redirect scheme https code 301 unless { ssl_fc }` line in the `teamcity-multinode-haproxy/haproxy/haproxy.cfg` file.