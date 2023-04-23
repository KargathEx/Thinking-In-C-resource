为了回答[一个关于重载operator<<的问题](https://stackoverflow.com/questions/74672391/get-private-element-outside-the-class)，翻了一下自己的笔记，想要确定是否`operator<<`必须且只能声明为`friend`.    

此书2000年以前出版的,虽然[amazon](https://www.amazon.com/Thinking-Vol-Introduction-Standard-2nd/dp/0139798099)说可以在[原作者](https://www.bruceeckel.com/) 的博客下载到源码，但我翻了八页都没看到一个关于`cpp`的标题。    
~~(可见影魔已经不适合这个版本了)~~    
[网站时光机](https://web.archive.org/web/20070304085516/http://www.mindview.net/FAQ/FAQ-002)保存了相关链接，以及官方答案。    

但是两个源码zip中都没有我以前存的c12文件夹，找了一圈最后还是从笔记中找到了当时fork的是谁的repo.    

写下几个理由，防止自己再删掉    
1.学习是资料的加工和整理的过程    
2.学习的目标是在需要的时候能正常回忆起来,记不住代码是正常的，以代码片段形式存在的笔记是有意义的.    
3.做题非必须，要善于利用出题者来归纳问题。    

原书online版本 [卷1](https://web.mit.edu/merolish/ticpp/TicV2.html),[卷2](https://web.mit.edu/merolish/ticpp/TicV2.html#_Toc53985710)
[another](https://www.fi.muni.cz/usr/jkucera/tic/tic_c.html)

#### 散记
1.[打印优先队列](https://www.linuxtopia.org/online_books/programming_books/c++_practical_programming/c++_practical_programming_189.html)只有两种办法:  
1)访问c然后sort [spec规定就是c](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/bits/stl_queue.h#L145)  
2.复制一份pqg,挨个pop()。
关于1)的方法如下:
```cpp
//: C07:PriorityQueue4.cpp
// Manipulating the underlying implementation.
class PQI : public priority_queue<int> {
public:
  vector<int>& impl() { return c; } 
  //c 是priority_queue中protected的
  //并且有标识符，根据标准C++规格说明，该标识符被命名为c
  //public继承来的除了基类的private,别的都可以摸,所以可以这样用.
};

int main() {
  PQI pqi;
  srand(time(0));
  for (int i = 0; i < 100; i++)
    pqi.push(rand() % 25);
  copy(pqi.impl().begin(), pqi.impl().end(),
    ostream_iterator<int>(cout, " "));
  cout << endl;
  while (!pqi.empty()) {
    cout << pqi.top() << ' ';
    pqi.pop();
  }
}

```
code from [here](https://www.linuxtopia.org/online_books/programming_books/c++_practical_programming/c++_practical_programming_189.html)   
想要print sorted的话还需要对复制的vector sort一下。   
如果考虑通用性,有个[长得好看版](https://stackoverflow.com/a/12886393/13792395)   
```cpp
template <class T, class S, class C>
S& Container(priority_queue<T, S, C>& q) {

  struct HackedQueue : private priority_queue<T, S, C>
  {
    static S& Container(priority_queue<T, S, C>& q) 
    {
      return q.* &HackedQueue::c;  // == q-> pointer,where pointer= (&HackedQueue::c)
    }
  };
  return HackedQueue::Container(q);
}
```
