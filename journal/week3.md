# Week 3 — Decentralized Authentication

## Congito JWT Server side Verify

Add to frontend-react >> src >> pages >> homefeedpage.js line 27 after const res = await and before method 'get'

```
  headers: {
    Authorization: `Bearer ${localStorage.getItem("access_token")}`
  }
  
 ```
 
 
