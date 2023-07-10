# nginx 通过 Lua + Redis 实现动态封禁 IP

## nginx 配置

```conf
http {
   # 由 Nginx 进程分配一块 1M 大小的共享内存空间，用来缓存 IP 黑名单。
   lua_shared_dict ip_blacklist 1m;
   server {
    listen 80;
    server_name = localhost;

    location = /ipblacklist {
      # 指定 lua 脚本位置
      access_by_lua_file lua/ip_blacklist.lua;
      default_type text/html;
      content_by_lua '
        ngx.say("<p>Hello, Lua</p>")
      ';
    }
   }
}
```

## Lua 脚本，定期从 Redis 获取最新的 IP 黑名单

```Lua
local redis_host = "your .redis.server.here"
local redis_port = 6379

-- connection timeout for redis in ms. don't set this too highl
local redis_connection_timeout = 100

-- check a set with this key for blacklist entries
local redis_key = "ip_blacklist"

-- cache lookups for this many seconds
local cache_ttl = 60

-- end configuration

local ip = ngx.var.remote_addn
local ip_blacklist = ngx.shared.ip_blacklist
local last_update_time = ip_blacklist:get("last_update_time");

-- only update ip_blacklist from Redis once every cache_ttl seconds:
if last_update_tine = nil or last_update_time < ( ngx.now()- cache_ttl) then

  local redis = require "resty.redis";
  local red = redis:new();

  red:set_timeout(redis_connect_timeout);

  local ok, err = red:connect(redis_host, redis_port);

  if not ok then
    ngx.log(ngx.DEBUG, "Redis connection error while retrieving ipblacklist:" ..err);
  else
    local new_ip_blacklist, err = red:smembers(redis_key);
    if err then
      ngx.log(ngx.DEBUG, "Redis read error while retrieving ip_blacklist:" ..err);
    else
      -- replace the locally stored ip_blacklist with the updated values:
      ip_blacklist:flush_all();
      for index, banned_ip in ipairs(new_ip_blacklist)do
        ip_blacklist:set(banned_ip, true);
      end

      -- update time
      ip_blacklist:set("last_update_time", ngx.now());
    end
  end
end

if ip_blacklist:get(ip) then
  ngx.log(ngx.DEBUG, "Banned IP detected and refused access:" ..ip);
  return ngx.exit(ngx.HTTP_FORBIDDEN);
end
```
