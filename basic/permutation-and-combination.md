## 排列

从n个不同元素中，任取m(m≤n,m与n均为自然数,下同）个元素按照一定的顺序排成一列

计算公式：![](https://imgsa.baidu.com/baike/s%3D257/sign=97158a763df33a879a6d071ff15d1018/2e2eb9389b504fc2974eb943e2dde71190ef6d66.jpg) ![](https://imgsa.baidu.com/baike/s%3D70/sign=c20e5a5165d0f703e2b297dc09fa9dc2/4ec2d5628535e5ddaf0ca99971c6a7efce1b6257.jpg)

## 组合

从n个不同元素中，任取m(m≤n）个元素并成一组

计算公式：![](https://imgsa.baidu.com/baike/s%3D162/sign=f68c65f4b5b7d0a27fc9009bf9ee760d/5d6034a85edf8db190ab75220e23dd54574e74ea.jpg)

C(n,m)=C(n,n-m）（n≥m)

C(n,m) = C(n-1,m) + C(n-1,m-1）


```
// ls = [1,2,3,4,5],k <= ls.length
function comb(ls, k) {
    const res = []
    for (let [i, v] of ls.entries()) {
        if (k === 1) {
            res.push([v])
            continue
        }
        const nextComb = comb(ls.slice(i + 1), k - 1)
        for (let arr of nextComb) {
            const next = arr
            next.unshift(v)
            res.push(next)
        }
    }
    return res
}

// [1,2,3]、[1,2,4]、[1,2,5]
// [1,3,4]、[1,3,5]
// [1,4,5]
// [2,3,4]、[2,3,5]
// [2,4,5]
// [3,4,5]
```
