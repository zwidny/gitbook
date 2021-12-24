# 本文主要就如何使用python并发

## ThreadPoolExecutor

注意对于顺序有要求的并发请确保处理顺序问题

```python
import concurrent.futures
import logging
import urllib.request
from multiprocessing import cpu_count

logging.basicConfig(level=logging.INFO)


def load_url(url, timeout):
    logging.info(f"load url: {url}")
    with urllib.request.urlopen(url, timeout=timeout) as conn:
        return conn.status


def batch_download_images(URLS):
    """由于是异步，处理的顺序不能保证， 如对顺序要求较高请尽量使用map来管理数据."""
    n_workers = min(32, cpu_count() + 4)
    with concurrent.futures.ThreadPoolExecutor(max_workers=n_workers) as executor:
        future_to_url = {executor.submit(load_url, url, 3): url for url in URLS}
        datas = dict(
            (future_to_url[future], future.result()) for future in concurrent.futures.as_completed(future_to_url))
    return datas


if __name__ == '__main__':
    URLS = [
        "https://www.baidu.com/",
        "https://www.so.com/",
        "https://cn.bing.com/",
    ]

    data = batch_download_images(URLS)
    print(data)

```

## multiprocessing
注意对于顺序有要求的并发请确保处理顺序问题, 另外注意参数问题
```python

import logging
import multiprocessing
import urllib.request
from multiprocessing.dummy import Pool

n_workers = min(2, multiprocessing.cpu_count() + 4)

logging.basicConfig(level=logging.INFO)


def load_url(url, timeout=10):
    logging.info(f"load url: {url}")
    with urllib.request.urlopen(url, timeout=timeout) as conn:
        return conn.status


def batch_download_images(URLS):
    """由于是多进程，处理的顺序不能保证，需要程序自身处理"""
    with Pool(n_workers) as p:
        return p.map(load_url, URLS)


if __name__ == '__main__':
    URLS = [
        "https://www.baidu.com/",
        "https://www.so.com/",
        "https://cn.bing.com/",
    ]
    data = batch_download_images(URLS)
    print(data)
```