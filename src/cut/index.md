# Cut

cut操作を書きます.

```cpp
void bring(node* root) {
  node* rake = root->rake();

  if(!rake) {
    node* left = root->child(0);
    //delete root, root = nullptr;
    left->set_parent(nullptr);
    left->fix();
  }
  else if(rake->type() == Type::Compress || rake->type() == Type::Edge) {
    rake->push();
    node* new_right = rake;
    new_right->reverse();
    new_right->push();

    root->set_child(new_right, 1);
    new_right->set_parent(root);

    root->set_rake(nullptr);

    new_right->fix();
    root->fix();
  }
  else if(rake->type() == Type::Rake) {
    rake->push();
    while(rake->child(1)->type() == Type::Rake) {
      rake->child(1)->push();
      rake = rake->child(1);
    }
    root->guard = true;
    splay(rake);
    root->guard = false;

    node* new_rake = rake->child(0);
    node* new_right = rake->child(1);

    //delete rake, rake = nullptr;
    new_right->reverse();
    new_right->push();

    root->set_child(new_right, 1);
    new_right->set_parent(root);

    root->set_rake(new_rake);
    new_rake->set_parent(root);

    new_rake->fix();
    new_right->fix();
    root->fix();
  }
}

void cut(vertex v, vertex u) {
  soft_expose(v, u);
  node* root = v.handle();
  root->push();
  node* right = root->child(1);
  right->set_parent(nullptr);

  right->reverse();
  right->push();

  bring(right);
  bring(root);
}

cluster path_query(vertex v, vertex u) {
  soft_expose(v, u);
  node* root = v.handle();
  root->push();
  if(root->endpoint(0) == v && root->endpoint(1) == u) {
    return root->fold();
  }
  else if(root->endpoint(0) == v) {
    return root->child(0)->fold();
  }
  else if(root->endpoint(1) == u) {
    return root->child(1)->fold();
  }
  else {
    root->child(1)->push();
    return root->child(1)->child(0)->fold();
  }
}
```
