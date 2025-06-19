## start a gateway

This gateway will be available at http://localhost:9011/sse (I think we discussed you using sse but we can also expose on http://localhost:9011/mcp)

This particular server makes three mcp servers available (brave, resend, wikipedia-mcp).  Of the tools, `brave_web_search` comes from the brave mcp, `send-email` comes from resend, and the others come from wikipedia-mcp.

Both brave and resend require api keys, so you need to create a `.env` file with the entries:

```
brave.api_key=
resend.api_key=
```

Feel free to leave these blank though. You'll just notice in the log output that the server could not start if the secrets are missing. Some MCP servers 
really need to fully connect before we can even get an accurate tool list.

```bash
docker run --name gateway \
           --rm --init \
           -v /var/run/docker.sock:/var/run/docker.sock \
           -v ./docker-mcp.yaml:/app/docker-mcp.yaml \
           -v ./config.yaml:/app/config.yaml \
           -v ./.env:/app/env \
           docker/agents_gateway:v2 \
           --verbose \
           --transport=sse \
           --catalog=/app/docker-mcp.yaml \
           --config=/app/config.yaml \
           --secrets=/app/env \
           --servers=brave \
           --servers=resend \
           --servers=wikipedia-mcp \
           --tools=brave_web_search \
           --tools=get_article \
           --tools=get_summary \
           --tools=get_related_topics \
           --tools=send-email \
           --port=9011
```

## catalog

There is a catalog file in this directory called `docker-mcp.yaml` and this contains the full definitions for the three mcp servers (brave, resend, wikipedia-mcp).  
You can add more servers to this file, or create gateway configurations with other sets of servers. Maybe we can use this repo to collaborate on some new catalog files.

## config

Not all mcp servers will have custom configuration but resend does, so I added an example of resend here.
