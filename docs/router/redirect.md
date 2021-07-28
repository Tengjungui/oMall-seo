```javascript
export default async function(req, res, next) {
  let redirects = [];
  const radioType = { 1: 301, 2: 302 };
  const api = basePath().apiPath;
  axios.defaults.headers.common["X-Client-Type"] = "PC";
  let { code, data } = (
    await axios.get(`${api}/config/api/seo/redirect/listAll`)
  ).data;
  // request to update the redirect list
  if (code === "200" && Array.isArray(data) && data.length) {
    redirects = redirects.concat(
      Array.from(data, ({ requestLink, targetLink, type }) => ({
        from: requestLink,
        to: targetLink,
        code: radioType[type]
      }))
    );
  }

  // find the redirect if it exists where the from === the requested url
  const redirect = redirects.find(r => r.from === req.url);

  // If it exists, redirect the page with a 301 response else carry on
  if (redirect) {
    res.writeHead(redirect.code, { Location: redirect.to });
    res.end();
  } else {
    next();
  }
}

```

