// singly linked list and stack 

typedef struct Node {
    Student* info;
    struct Node* next;
} Node;

// STACK PUSH / ADD START
Node* insertStart(Node* head, Student* s) {
    Node* nn = (Node*)malloc(sizeof(Node));
    nn->info = s;
    nn->next = head;
    return nn;
}

// ADD END
Node* insertEnd(Node* head, Student* s) {
    Node* nn = (Node*)malloc(sizeof(Node));
    nn->info = s;
    nn->next = NULL;
    if (!head) return nn;
    Node* t = head;
    while (t->next) t = t->next;
    t->next = nn;
    return head;
}

// DEALLOCATE
void deallocateSLL(Node** head) {
    while (*head) {
        Node* aux = *head;
        *head = (*head)->next;
        deleteStudent(aux->info);
        free(aux);
    }
}

// TASK: Count students in a specific group
int countByGroup(Node* head, int gNo) {
    int count = 0;
    while (head) {
        if (head->info->groupNo == gNo) count++;
        head = head->next;
    }
    return count;
}

// doubly linked list 

typedef struct DoubleNode {
    Student* info;
    struct DoubleNode *next, *prev;
} DoubleNode;

typedef struct {
    DoubleNode *head, *tail;
} DoubleList;

// INSERT TAIL
DoubleList insertTail(DoubleList list, Student* s) {
    DoubleNode* nn = (DoubleNode*)malloc(sizeof(DoubleNode));
    nn->info = s;
    nn->next = NULL;
    nn->prev = list.tail;
    if (list.tail) list.tail->next = nn;
    else list.head = nn;
    list.tail = nn;
    return list;
}

// DEALLOCATE
void deallocateDLL(DoubleList* list) {
    DoubleNode* curr = list->head;
    while (curr) {
        DoubleNode* aux = curr;
        curr = curr->next;
        deleteStudent(aux->info);
        free(aux);
    }
    list->head = list->tail = NULL;
}

//circular singly linked list 
// ALPHABETICAL INSERT (Circular)
Node* insertCircularSorted(Node* head, Student* s) {
    Node* nn = (Node*)malloc(sizeof(Node));
    nn->info = s;
    if (!head) { nn->next = nn; return nn; }

    // Case: New head (alphabetically first)
    if (strcmp(s->name, head->info->name) < 0) {
        Node* t = head;
        while (t->next != head) t = t->next;
        nn->next = head;
        t->next = nn;
        return nn;
    }

    // Case: Middle or End
    Node* t = head;
    while (t->next != head && strcmp(t->next->info->name, s->name) < 0)
        t = t->next;
    nn->next = t->next;
    t->next = nn;
    return head;
}

// TASK: Count Unique Groups
int countUnique(Node* head) {
    if (!head) return 0;
    int seen[10000] = {0}, count = 0;
    Node* t = head;
    do {
        if (seen[t->info->groupNo] == 0) {
            count++;
            seen[t->info->groupNo] = 1;
        }
        t = t->next;
    } while (t != head);
    return count;
}

//queue  (circular single tail)
// PUT (Enqueue)
Node* put(Node* tail, Student* s) {
    Node* nn = (Node*)malloc(sizeof(Node));
    nn->info = s;
    if (!tail) { nn->next = nn; return nn; }
    nn->next = tail->next; // Point to head
    tail->next = nn;
    return nn; // New tail
}

// GET (Dequeue)
Student* get(Node** tail) {
    if (!*tail) return NULL;
    Node* head = (*tail)->next;
    Student* s = head->info;
    if (head == *tail) *tail = NULL;
    else (*tail)->next = head->next;
    free(head);
    return s;
}

//hash table (chaining)
typedef struct {
    Node** items;
    int size;
} HashTable;

// INITIALIZE
HashTable createHT(int size) {
    HashTable ht;
    ht.size = size;
    ht.items = (Node**)calloc(size, sizeof(Node*));
    return ht;
}

// INSERT
void insertHT(HashTable ht, Student* s) {
    int idx = s->regNo % ht.size;
    Node* nn = (Node*)malloc(sizeof(Node));
    nn->info = s;
    nn->next = ht.items[idx];
    ht.items[idx] = nn;
}

// DEALLOCATE
void deallocateHT(HashTable ht) {
    for (int i = 0; i < ht.size; i++) {
        Node* curr = ht.items[i];
        while (curr) {
            Node* aux = curr;
            curr = curr->next;
            deleteStudent(aux->info);
            free(aux);
        }
    }
    free(ht.items);
}

//main
int main() {
    // 1. CHOOSE YOUR STRUCTURE SETUP:
    Node* head = NULL;                  // SLL / CSLL / Stack / Queue
    DoubleList dList = {NULL, NULL};    // DLL
    HashTable ht = createHT(101);       // Hash Table

    FILE* pFile = fopen("Data.txt", "r");
    if (pFile) {
        char line[256], *token, *context;
        char delim[] = {',','\n','\0'};
        
        while (fgets(line, 256, pFile)) {
            token = strtok_s(line, delim, &context);
            unsigned int reg = atoi(token);
            token = strtok_s(NULL, delim, &context);
            unsigned short group = atoi(token);
            token = strtok_s(NULL, delim, &context);
            
            Student* s = createStudent(reg, group, token);

            // 2. CHOOSE YOUR INSERT HOOK:
            // head = insertStart(head, s);
            // head = insertCircularSorted(head, s);
            // dList = insertTail(dList, s);
            // insertHT(ht, s);
        }
        fclose(pFile);
    }

    // 3. DO TASKS & CLEANUP HERE
    return 0;
}
