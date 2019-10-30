# Soft Expose

Soft Expose操作を書きます.

```cpp
void soft_expose(vertex v, vertex u) {
  node* root = expose(v);
  if(v.handle() == u.handle()) {
    if(root->endpoint(1) == v || root->endpoint(0) == u) {
      root->reverse();
      root->push();
    }
    return;
  }
  root->guard = true;
  node* soot = expose(u);
  root->guard = false;
  root->fix();
  if(parent_dir(soot) == 0) {
    root->reverse();
    root->push();
  }
}
```
