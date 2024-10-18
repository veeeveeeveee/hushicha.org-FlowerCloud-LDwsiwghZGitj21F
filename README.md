
好家伙,


 


# 0\.错误描述


**今天在开发中犯了一个比较严重的错误**


**对于Promise的错误使用**


 


**场景:**


**微信小程序中展示搜索条件列表**




```
// API请求工具函数
const apiRequest = (url, method = 'GET', headers = {}) => {
  return new Promise((resolve, reject) => {
    wx.request({
      url,
      method,
      header: headers,
      success: (res) => resolve(res),
      fail: (error) => reject(error)
    });
  });
};

const fetchTypelist = async () => {
  const app = getApp();
  const queryParams = app.globalData.queryParams;
  console.log(queryParams)
  let url = `${API_BASE_URL}/industryType/page?pageNo=1&pageSize=10`;

  // 添加常规的查询参数
  for (const [key, value] of Object.entries(queryParams)) {
    if (value) {
      url += `&${key}=${encodeURIComponent(value)}`;
    }
  }
  try {
    const headers = {}; // 如果需要token,可以在这里添加
    const response = await apiRequest(url, 'GET', headers);

    if (response.data.code === 200) {
      console.log(response.data)
      return response
    } else {
      throw new Error('获取地址列表失败');
    }
  } catch (error) {
    throw error;
  }
};

/.................../
调用方法

let result = fetchTypelist()
console.log(result)
```


 



**猜猜会打印什么?**


**![](https://img2024.cnblogs.com/blog/2501855/202410/2501855-20241017161043974-1698290119.png)**


**一个promise是的仅仅只是一个promise,并没有拿到请求返回的结果**



# **1\.分析**


**`async` 函数默认返回一个 `Promise`，即使你在 `async` 函数中显式返回了值。**


**当你调用 async定义的函数 `(fetchTypelist)` 时，返回的实际上是一个 `Promise`，而不是直接返回的数据。**


 


# **2\.解决方法**


**2\.1\.使用await等待方法的执行，获取返回值**




```
const getTypelist = async () => {
  try {
    const typelist = await fetchTypelist();
    console.log(typelist); // 这里可以获取到返回的 rows
  } catch (error) {
    console.error(error); // 处理错误
  }
};
```


 


 


**2\.2\.使用链式调用.then()来处理promise**




```
fetchTypelist()
  .then((typelist) => {
    console.log(typelist);
  })
  .catch((error) => {
    console.error(error);
  });
```


 


 


 


# 3\.成功获取


![](https://img2024.cnblogs.com/blog/2501855/202410/2501855-20241017173003236-1693218227.png)


 


 本博客参考[楚门加速器p](https://tianchuang88.com)。转载请注明出处！
