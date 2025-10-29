{
  "log": {
    "disabled": false,
    "level": "info",
    "timestamp": true
  },
  
  "dns": {
    "servers": [
      {"tag": "localDNS", "address": "119.29.29.29", "detour": "direct"},
      {"tag": "defaultDNS", "address": "114.114.114.114", "detour": "direct"},
    ]
    "independent_cache": true,
    "strategy": "ipv4_only"
  },
  
  "inbounds": [
    {
      "type": "tun",
      "tag": "tun-in",
      "interface_name": "sing-tun",
      "auto_route": true,
      "strict_route": false,
      "stack": "gvisor"
    }
  ],
  
  "outbounds": [
    {"tag": "Proxy", "type": "selector", "outbounds": ["{all}"]},
    {"tag": "Google", "type": "selector", "outbounds": ["香港", "美国"] },
    {"tag": "香港", "type": "urltest", "outbounds": ["{all}"], "filter": [{ "action": "include", "keywords": ["HKG"] }], "url": "http://cp.cloudflare.com/generate_204", "interval": "10m", "tolerance": 0 },
    {"tag": "美国", "type": "urltest", "outbounds": ["{all}"], "filter": [{ "action": "include", "keywords": ["USA"] }], "url": "http://cp.cloudflare.com/generate_204", "interval": "10m", "tolerance": 0 },
    {"tag": "Direct", "type": "direct"},
    {"tag": "Block", "type": "block" }
  ],
  
  "route": {
    "rules": [
      { "rule_set": ["geoip-google", "geosite-google"], "outbound": "Google" },
      { "rule_set": ["geoip-apple", "geosite-apple"], "outbound": "Direct" },
      { "rule_set": ["geoip-cn", "geosite-cn"], "outbound": "Direct" }
    ]
    "rule_set": [
      { "tag": "geosite-google", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/google.srs", "download_detour": "Proxy" },
      { "tag": "geoip-google", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/google.srs", "download_detour": "Proxy" },
      { "tag": "geosite-apple", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/apple.srs", "download_detour": "Proxy" },
      { "tag": "geoip-apple", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo-lite/geoip/apple.srs", "download_detour": "Proxy" },
      { "tag": "geosite-cn", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/cn.srs", "download_detour": "Proxy" },
      { "tag": "geoip-cn", "type": "remote", "format": "binary", "url": "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/cn.srs", "download_detour": "Proxy" }
    ]
    "final": "proxy",
    "auto_detect_interface": true
  },
}
