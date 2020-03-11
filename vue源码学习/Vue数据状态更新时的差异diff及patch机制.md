## Vue数据状态更新时的差异diff及patch机制

#### 数据更新视图

- 在对model进行操作时，会触发对应Dep中的Watcher对象。Watcher对象会调用对应的update来修改视图。最终是将新产生的VNode节点与老VNode节点进行一个patch的过程，对比得出差异。最终将这些差异更新到视图上



#### 跨平台

- Virtual DOM，一些javaScript对象

- 依赖一层适配器，将不同平台的API封装在内，以同样的接口对外提供

  ```js
  const nodeOps = {
    //根据platform区分不同平台来执行当前平台对应的API
    setTextContent (text) {
      if (platform === 'weex') {
        node.parentNode.setAttr('value', text);
      } else if (platform === 'web') {
        node.textContent = text;
      }
    },
    parentNode () {
      
    },
    removechild () {
      
    },
    nextSibling () {
      
    },
    insertBefore () {
      
    }
  }
  ```

- insert用来在parent这个父节点下插入一个子节点，如果指定来ref则插入到ref这个子节点前面

  ```js
  function insert (parent, elm, ref) {
    if (parent) {
      if (ref) {
        if (ref.parentNode === parent) {
          nodeOps.insertBefore(parent, elm, ref);
        }
      } else {
        nodeOps.appendchild(parent, elm);
      }
    }
  }
  
  //createElm用来创建一个节点，tag存在则创建一个标签节点，否则创建一个文本节点
  function createElm (vnode, parentElm, refElm) {
    if (vnode.tag) {
      insert (parentElm, nodeOps.createElement(vnode.tag), refElm);
    } else {
      insert(parentElm, nodeOps.createTextNode(vnode.text), refElm);
    }
  }
  
  //addVnodes用来批量调用createElm新建节点
  function addVnodes (parentElm, refElm, vnodes, startIdx, endIdx) {
    for (; startIdx <= endIdx; ++startIdx) {
      createElm(vnode[startIdx], parentelm, refElm);
    }
  }
  
  //removeNodes用来移除一个节点
  function removeNode (el) {
    const parent = nodeOps.parentNode(el);
    if (parent) {
      nodeOps.removeChild(parent, el);
    }
  }
  
  //removeVnodes会批量调用removeNode节点
  function removeVnodes (parentElm, vnodes, startIdx, endIdx) {
    for (; startIdx <= endIdx; ++startIdx) {
      const ch = vnodes[startIdx];
      if (ch) {
        removeNode(ch.elm);
      }
    }
  }
  
  ```

- ### patch-只进行同级的比较

  - diff算法是通过同层的树节点进行比较，而不是对树进行逐层搜索遍历的方式，时间复杂度为O(n)
  - 主要功能：比较两个VNode节点，将差异更新到视图上

  ```js
  function patch (oldVnode, vnode, parentElm) {
    if (!oldVnode) { //若旧节点不存在，则新增新节点
      addVnodes(parentElm, null, vnode, 0, vnode.length - 1);
    } else if (!vnode) { //若新节点不存在，则删除旧节点
      removeVnodes(parentElm, oldVnode, 0, oldVnode.length - 1);
    } else {
      if (sameVnode(oldVnode, vnode)) { //如果两个节点类型相同，则patch更新
        patchVnode(oldVnode, vnode)
      } else { //如果节点类型不相同，则删除旧节点，新增新节点
        removeVnodes(parentElm， oldVnode, 0, oldVnode.length - 1);
        addVnodes(parentElm, null, vnode, 0, vnode.length - 1)
      }
    }
  }
  
  //!!a.data 判断a.data不是null或undefined
  //只有当key、tag、isComment(是否为注释节点)、data同时定义(或不定义),同时满足当标签类型为input的时候type相同
  function sameVnode (a,b) {
    return (
    	a.key === b.key && a.tag === b.tag && a.isComment === b.isComment && (!!a.data) === (!!b.data) && sameInputType(a,b)
    )
  }
  
  function sameInputType (a, b) {
    if (a.tag !== 'input') return true;
    let i;
    const typeA = (i = a.data) && (i = i.attrs) && i.type;
    const typeB = (i = b.data) && (i = b.data) && i.type;
    return typeA === typeB;
  }
  
  //只有当节点类型相同时，才进行比较
  function patchVnode (oldVnode, vnode) {
    if (oldVnode === vnode) {
      return;
    }
    
    //若新旧节点都是静态节点，并且新旧节点的key值相同
    if (vnode.isStatic && oldVnode.isStatic && vnode.key === oldVnode.key) {
      vnode.elm = oldVnode.elm;
      vnode.componentInstance = oldVnode.componentInstance;
      return;
    }
    //vnode.elm = oldvnode.elm 和 elm = vnode.elm
    const elm = vnode.elm = oldvnode.elm; 
    const oldcCh = oldVnode.children; //旧节点的孩子节点
    const ch = vnode.children; //新节点的孩子节点
    
    if(vnode.text) {  //若新节点是文本节点,直接设置文本
      nodeOps.setTextContent(elm, vnode.text);
    } else { //若新节点非文本节点
      if (oldCh && ch && (oldCh !== ch)) { //若子节点不同，则更新
        updateChildren(elm, oldCh, ch);
      } else if (ch) { //只有当新孩子节点存在时
        //若旧节点是文本节点，则先将旧节点的文本清除
        if (oldVnode.text) nodeOps.setTextContent(elm, '');
        //将ch批量插入到节点elm下
        addVnodes(eml, null, ch, 0, ch.length - 1);
      } else if (oldCh) { //若旧节点孩子节点存在，删除所有孩子节点
        removeVnodes(elm, oldCh, 0, ch.length - 1);
      } else if (oldVnode.text) { //当旧节点是文本节点时，清除其节点文本内容
        nodeOps.setTextContent(elm, '');
      }
    }
  }
  ```

- ### updateChildren

  ```js
  //更新孩子节点
  function updateChildren (parentElm, oldCh, newCh) {
    let oldStartIdx = 0; //旧节点开始index
    let newStartIdx = 0; //新节点开始index
    let oldEndIdx = oldCh.length - 1; //旧孩子节点长度
    let oldStartVnode = oldCh[0];  //第一个旧孩子节点
    let oldEndVnode = old[oldEndIdx]; //最后一个旧孩子节点
    let newEndIdx = newCh.length - 1; //新孩子节点长度
    let newStartVnode = newCh[0];  //第一个新孩子节点 
    let newEndVnode = newCh[newEndIdx]; //最后一个新孩子节点
    let oldKeyToIdx, idxInOld, elmToMove, refElm;
    
    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
      //1、首先当oldStartVnode或者oldEndVnode不存在的时候，oldStartIdx与oldEndIdx继续向中间靠拢
      if (!oldStartVnode) {
        oldStartVnode = oldCh[++oldStartIdx];
      } else if (!oldEndVnode) {
        oldEndVnode = oldCh[--oldEndIdx];
        
        //2、将oldStartIdx、newStartIdx、oldEndIdx以及newEndIdx两两对比过程，共四种情况
      } else if (sameVnode(oldStartVnode, newStartVnode)) {
        //头头比较
        patchVnode(oldStartVnode, newStartVnode); 
        oldStartVnode = oldCh[++oldStartIdx];
        newStartVnode = newCh[++newStartIdx];
        //尾尾比较
      } else if (sameVnode(oldEndVnode, newEndVnode)) {
        patchVnode(oldEndVnode, newStartVnode);
        oldEndVnode = oldCh[--oldEndIdx];
        newEndVnode = newCh[--newEndIdx];
        //头尾比较
      } else if (sameVnode(oldStartVnode, newEndVnode)) {
        patchVnode(oldStartVnode, newEndVnode);
        //将旧节点的第一个孩子节点移到最后一个
        nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm));
       oldStartVnode = oldCh[++oldStartIdx];
       newEndVnode = newCh[--newEndIdx];
        //尾头比较
      } else if (sameVnode(oldEndVnode, newStartVnode)) {
        patchVnode(oldEndVnode, newStartVnode);
        //将旧节点的最后一个孩子节点移到第一个
        nodeOps.insertBefore(parentElm, oldEndVnode.elm, nodeStartVnode.eml)
        oldEndVnode = oldCh[--oldEndIdx];
        newStartVnode = newCh[++newStartIdx];
      } else {
        // oldKeyToIdx, idxInOld, elmToMove, refElm
        let elmToMove = oldCh[idxInOld];
        if (!oldKeyToIdx) {
          oldKeyToIdx = createKeyToIdx(oldch, oldStartIdx, oldEndIdx);
        }
        idxInOld = newStartVnode.key ? oldKeyToIdx[newStartVnode.key] : null; //记录在旧孩子节点中与第一个新孩子key相同的索引
        if (!idxInOld) {  //如果没有key相同的孩子节点
          createElm(newStartVnode, parentElm);
          newStartVnode = newCh[++newStartIdx];
        } else { //如果新旧孩子中，存在key相同的孩子
          elmToMove = oldCh[idxInOld]; //记录旧节点中需要移动的孩子节点
          if (sameVnode(elmMove, newStartVnode)) {
            patchVnode(elmMove, newStartVnode);
            oldCh[idxInOld] = undefined;
            nodeOps.insertBefore(parentElm, newStartVnode.elm, oldStartVnode.elm);
            newStartVnode = newCh[++newStartIdx];
          } else { //如果节点类型不一样，创建一个新节点插入到parentElm的子节点中，newStartIdx往后移动一位
            createElm(newStartVnode, parentElm);
            newStartVnode = newCh[++newStartIdx];
          }
        }
      }
    }
    
    //说明旧节点比较结束，但是新节点还有，则需要将剩余新节点插入到真实DOM中
    if(oldStartIdx > oldEndIdx) {
      refElm = (newCh[newEndIdx + 1]) ? newCh[newEndIdx + 1].elm : null;
      addVnodes(parentElm, refElm, newCh, newStartIdx, newendIdx);
    } else if (newStartIdx > newEndIdx) {
      removeVnodes(parentElm, oldCh, oldStartIdx, oldEndIdx);
    }
    
  }
  
  //产生key与index索引对应的一个map表
  //可以根据key值，快速地从oldKeyToIdx的返回值中获取相同的key的节点的索引idxInOld,然后找到相同的节点
  function createKeyToIdx (children, beginIdx, endIdx) {
    let i, key;
    const map = {};
    for (i = beginIdx; i <= endIdx; ++i) {
      key = children[i].key;
      if (isDef(key)) map[key] = i;
    }
    return map;
  }
  ```

  