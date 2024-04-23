## Boilerplate Code
```c
#include <stdio.h>
#include <stdio.h>
#include <assert.h>

# define MAX(a, b) (((a) < (b)) ? (b) : (a))

typedef struct Node Node;

struct Node {
	int data;
	Node *left, *right, *parent;
	int height;
	int bf;
}

Node *createNode(int data) {
	Node *res;

	res = (Node *) malloc(sizeof(Node));

	res->data = data;
	res->left = res->right = res->parent = NULL;

	res->height = 0;
	res->bf = 0;

	return res;
}

int getHeight(Node *root) {
	if(root == NULL) {
		return -1;
	}

	return root->height;
}
```

## Balance Factor Function
```c
void recompBF(Node * root) {
	if (root == NULL) return;

	int leftHeight = getHeight(root->left);
	int rightHeight = getHeight(root->right);
	root->height = MAX(leftHeight, rightHeight) + 1;
	root->bf = leftHeight - rightHeight;
}
```

## Left Rotation
```c
Node *rotateLeft(Node *rot) {
	// A left rotation means a right child must exist
	assert(rot && rot->right);

	// Get pointers to the parent, child, and grandchild
	Node *par = rot->parent; // parent
	Node *ch = rot->right; // child
	Node *gc = ch->left; // grandchild

	// Top Edge
	ch->parent = parent;
	// Check parent's existence
	if(par) {
		// Check the side of the rotated node for its parent
		if(par->right == rot) {
			par->right = ch;
		} else {
			par->left = ch;
		}
	}

	// Middle Edge
	ch->left = rot;
	rot->parent = ch;

	// Bottom edge
	rot->right = gc;
	if(gc) { // check grandchild's existence
		gc->parent = rot;
	}

	// Recompute the balance factors (from bottom to top)
	recompBF(rot);
	recombBF(ch);

	return ch;
} 
```

## Right Rotation
```c
Node *rotateRight(Node *rot) {
	// A left rotation means a right child must exist
	assert(rot && rot->left);

	// Get pointesr to the parent, child, and grandchild
	Node *par = rot->parent;
	Node *ch = rot->left;
	Node *gc = ch->right;

	// Top Edge
	ch->parent = par;
	// Check parent's existence
	if(par) {
		// Check the side of the rotated node for its parent
		if(par->right == rot) {
			par->right = ch;
		} else {
			par->left = ch;
		}
	}

	// Middle edge
	ch->right = rot;
	rot->parent = ch;

	// Bottom edge
	rot->left = gc;
	if(gc) { // check grandchild's existence
		gc->parent = rot;
	}

	// Recompute balance factors (from bottom to top) 
	recompBF(rot);
	recompBF(ch);

	return ch;
}
```

## Remove from AVL Tree
```c
Node *removeAVL(Node *root, int data) {
	Node *cur = root;

	// search for the value we are looking to remove
	while(cur && cur->data != data) {
		if(cur->data < data) {
			// Go right
			cur = cur->right;
		} else if(cur->data > data) {
			// Go left
			cur = cur->left;
		}
	}

	// Value not in tree
	if(!cur) return root;

	// Node should be removed
	Node *par = cur->parent;

	if(cur->left == NULL || cur->right == NULL) {
		// 1 child case (also handles 0 child case)
		Node *child = cur->right;
		if(!child) {
			child = cur->left;
		}

		free(cur);
		if(child) child->parent = par;
		if(!par) {
			// Tree's root is now child
			return child;
		}
		if(par->left == cur) {
			par->left = child;
		} else {
			par->right = child;
		}

		// Balance tree up to root
		while(par) {
			recompBF(par);
			if(par->bf == 2) {
				// LEFT HEAVY
				if(par->left->bf < 0) {
					// RIGHT HEAVY
					rotateLeft(par->left);
				}
				rotateRight(par);
			}
			if(par->bf == -2) {
				// RIGHT HEAVY
				if(par->right->bf > 0) {
					// LEFT HEAVY
					rotateRight(par->right);
				}
				rotateLeft(par);
			}
			root = par;
			par = par->parent;
		}
		return root;
	}
	// 2 child case
	Node *pred = cur->left
	while(pred->right) {
		pred = pred->right;
	}

	// Swap with pred
	int tmp = cur->data;
	cur->data = pred->data;
	pred->data = tmp;

	// Remove the data from the subtree
	return removeAVL(cur->left, data);
}
```

## Insert AVL
```c
Node *insertAVL(Node *root, int data) {
	// Empty tree case
	if(root == NULL) return createNode(data);

	// Child the parent for the insert
	Node *parent = NULL
	Node *child = root;
	while(child) {
		if(child->data < data) {
			// GO RIGHT
			parent = child;
			child = parent->right;
		} else if(child->data > data) {
			// GO LEFT
			parent = child;
			child = parent->left;
		} else {
			// DUP NODE
			return root;
		}
	}

	// Insert at the location with parent as parent
	if(parent->data < data) {
		parent->right = createNode(data);
		parent->right->parent = parent;
	} else {
		parent->left = creatreNode(data);
		parent->left->parent = parent;
	}

	// Rebalance from node
	while(parent) {
		recompBF(parent);
		if(parent->bf == 2) {
			// LEFT HEAVY
			if(parent->left->bf < 0) {
				// RIGHT HEAVY
				rotateLeft(parent->left);
			}
			rotateRight(parent);
			break;
		}
		if(parent->bf == -2) {
			// RIGHT HEAVY
			if(parent->right->bf > 0) {
				// LEFT HEAVY
				rotateRight(parent->right);
			}
			rotateLeft(parent);
			break;
		}
		parent = parent->parent;
	}

	// Find the new root
	while(root->parent) {
		root = root->parent;
	}

	return root;
}
```