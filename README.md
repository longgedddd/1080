# 1080
// remove_sections.js
var body = $response.body;
if (body) {
  try {
    var obj = JSON.parse(body);
    if (obj && obj.data && Array.isArray(obj.data.sections_v2) && obj.data.sections_v2.length > 1) {
      obj.data.sections_v2[1].title = null;
      var items = obj.data.sections_v2[1].items;
      if (Array.isArray(items)) {
        var blacklist = ["联系客服", "稿件管理", "我的钱包", "我的NFT"];
        obj.data.sections_v2[1].items = items.filter(function(it){
          if (!it || typeof it.title !== "string") return true;
          return blacklist.indexOf(it.title) === -1;
        });
      }
    }
    $done({ body: JSON.stringify(obj) });
  } catch (e) {
    $done({ body: body });
  }
} else {
  $done({});
}
