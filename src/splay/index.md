# Splay

splay操作を実装します

```cpp
int parent_dir(node* child) {
  node* par = child->parent();
  if(par) {
    if(par->child(0) == child) { return 0; }
    else if(par->child(1) == child) { return 1; }
    else { return -1; }
  }
  else { return -1; }
}

void rotate(node* t, node* x, std::size_t dir) {
  node* y = x->parent();
  int par = parent_dir(x);
  t->child(dir)->push();
  x->set_child(t->child(dir), dir ^ 1);
  t->child(dir)->set_parent(x);
  t->set_child(x, dir);
  x->set_parent(t);
  t->set_parent(y);
  if(par != -1) {
    y->set_child(t, par);
  }
  else if(y && y->type() == Type::Compress) {
    y->set_rake(t);
  }
  x->fix();
  t->fix();
  if(y) { y->fix(); }
}

void splay(node* t) {
  assert(t->type() != Type::Edge);
  t->push();

  while(parent_dir(t) != -1) {
    node* q = t->parent();
    if(q->type() != t->type()) break;
    if(parent_dir(q) != -1 && q->parent() && q->parent()->type() == q->type()) {
      node* r = q->parent();
      if(r->parent()) r->parent()->push();
      r->push();
      q->push();
      t->push();
      int qt_dir = parent_dir(t);
      int rq_dir = parent_dir(q);
      if(rq_dir == qt_dir) {
        rotate(q, r, rq_dir ^ 1);
        rotate(t, q, qt_dir ^ 1);
      }
      else {
        rotate(t, q, qt_dir ^ 1);
        rotate(t, r, rq_dir ^ 1);
      }
    }
    else {
      if(q->parent()) q->parent()->push();
      q->push();
      t->push();
      int qt_dir = parent_dir(t);
      rotate(t, q, qt_dir ^ 1);
    }
  }
}
```
