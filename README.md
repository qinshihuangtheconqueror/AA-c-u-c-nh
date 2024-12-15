//c1
int main() {
    int n;
    std::cin >> n;

    int arr[n];
    for (int i = 0; i < n; i++) {
        std::cin >> arr[i];
    }

    int min = INT_MAX;
    for (int i = 0; i < n; i++) {
        if (arr[i] < min) {
            min = arr[i];
        }
    }

    std::cout << min << std::endl;

    return 0;
}
//c2
int main() {
    int n, D;
    std::cin >> n >> D;
    std::vector<int> a(n);
    for (int i = 0; i < n; i++) {
        std::cin >> a[i];
    }
    int count = 0;
    int sum = 0;
    int left = 0;

    for (int right = 0; right < n; right++) {
        sum += a[right];

        while (sum > D && left <= right) {
            sum -= a[left];
            left++;
        }
        count += (right - left + 1);
    }
    std::cout << count << std::endl;
    return 0;
}
// c4 ( day con chan le)
#include <bits/stdc++.h>
#define lo long long
#define KKH "file"
#define fi first
#define se second
#define BIT(mask,k) (((mask)>>(k-1))&1)
#define rs(x,a) memset(x,(a),sizeof x)
#define off(mask,k) ((mask)^(1<<(k-1)))
#define len(a) int(a.size())
#define all(a) a.begin(),a.end()
using namespace std;
const int mod=1e9+7;
const int gh=1005;
int n,a[gh],kq,dp[gh];
int get(int x)
{
    memset(dp,0,sizeof(dp));
    for (int i=1;i<=n;i++)
    if (a[i]%2==x) {
        dp[i]=1;
        for (int j=1;j<i;j++)
        if (a[j]%2==x && a[i]>=a[j]) {
            dp[i]=max(dp[i],dp[j]+1);
        }
        kq=max(kq,dp[i]);
    }
    return kq;
}
int main()
{
    cin>>n;
    for (int i=1;i<=n;i++)
        cin>>a[i];
    cout<<max(get(0),get(1));
}
// c4 bac thang

int countWays(int n) {
    if (n == 0) return 1;
    if (n == 1) return 1;
    if (n == 2) return 2;
    if (n == 3) return 4;

    vector<int> dp(n + 1, 0);
    dp[0] = 1;
    dp[1] = 1;
    dp[2] = 2;
    dp[3] = 4;

    for (int i = 4; i <= n; ++i) {
        dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];
    }

    return dp[n];
}

int main() {
    int n;
    cin >> n;
    cout << countWays(n) << endl;
    return 0;
}

//c3
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

const int MAX_M = 10;
const int MAX_N = 30;

int m, n;
vector<vector<int>> canTeach(MAX_M);
vector<int> credits;
vector<int> minClasses;
vector<pair<int,int>> conflicts;
vector<int> assignment;
bool found = false;

bool isConflict(int class1, int class2, int teacher) {
    for(auto& p : conflicts) {
        if((p.first == class1 && p.second == class2) || 
           (p.first == class2 && p.second == class1))
            return true;
    }
    return false;
}

bool canAssign(int classId, int teacher) {
    if(find(canTeach[teacher].begin(), canTeach[teacher].end(), classId+1) == canTeach[teacher].end())
        return false;
        
    for(int i = 0; i < n; i++) {
        if(assignment[i] == teacher && isConflict(i+1, classId+1, teacher))
            return false;
    }
    return true;
}

bool checkMinClasses() {
    vector<int> count(m, 0);
    for(int i = 0; i < n; i++) {
        if(assignment[i] != -1)
            count[assignment[i]]++;
    }
    for(int i = 0; i < m; i++) {
        if(count[i] < minClasses[i])
            return false;
    }
    return true;
}

void solve(int classId, int maxAllowedLoad) {
    if(found) return;
    if(classId == n) {
        if(checkMinClasses()) {
            found = true;
        }
        return;
    }

    for(int t = 0; t < m; t++) {
        if(canAssign(classId, t)) {
            assignment[classId] = t;
            
            vector<int> tempLoad(m, 0);
            for(int i = 0; i <= classId; i++) {
                if(assignment[i] != -1)
                    tempLoad[assignment[i]] += credits[i];
            }
            
            if(*max_element(tempLoad.begin(), tempLoad.end()) <= maxAllowedLoad) {
                solve(classId + 1, maxAllowedLoad);
            }
            
            if(found) return;
            assignment[classId] = -1;
        }
    }
}

int main() {
    cin >> m >> n;
    
    for(int i = 0; i < m; i++) {
        int p;
        cin >> p;
        for(int j = 0; j < p; j++) {
            int x;
            cin >> x;
            canTeach[i].push_back(x);
        }
    }
    
    credits.resize(n);
    for(int i = 0; i < n; i++) {
        cin >> credits[i];
    }
    
    minClasses.resize(m);
    for(int i = 0; i < m; i++) {
        cin >> minClasses[i];
    }
    
    int k;
    cin >> k;
    for(int i = 0; i < k; i++) {
        int p, q;
        cin >> p >> q;
        conflicts.push_back({p, q});
    }
    
    int left = *max_element(credits.begin(), credits.end());
    int right = accumulate(credits.begin(), credits.end(), 0);
    int result = -1;
    
    while(left <= right) {
        int mid = (left + right) / 2;
        assignment = vector<int>(n, -1);
        found = false;
        
        solve(0, mid);
        
        if(found) {
            result = mid;
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    
    cout << result << endl;
    return 0;
}
//c5
#include <iostream>
#include <vector>
#include <limits>
#include <algorithm>
#include <queue>

using namespace std;

// Function to perform topological sort using Kahn's algorithm
bool topologicalSort(const vector<vector<int>>& graph, vector<int>& order) {
    int n = graph.size();
    vector<int> in_degree(n, 0);
    
    // Compute in-degree for each node
    for (int i = 0; i < n; ++i) {
        for (int j : graph[i]) {
            in_degree[j]++;
        }
    }

    queue<int> q;
    for (int i = 0; i < n; ++i) {
        if (in_degree[i] == 0) {
            q.push(i);
        }
    }

    while (!q.empty()) {
        int node = q.front();
        q.pop();
        order.push_back(node);

        for (int neighbor : graph[node]) {
            if (--in_degree[neighbor] == 0) {
                q.push(neighbor);
            }
        }
    }

    return order.size() == n;
}

int main() {
    int n, m;
    cin >> n >> m; // n: number of cities, m: number of precedence constraints

    vector<vector<int>> dist(n, vector<int>(n));
    vector<vector<int>> precedence_graph(n); // Directed graph to hold precedence constraints
    vector<int> order; // This will store the topologically sorted order

    // Input the distance matrix
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            cin >> dist[i][j];
        }
    }

    // Input precedence constraints
    for (int i = 0; i < m; ++i) {
        int u, v;
        cin >> u >> v;
        u--; v--; // Convert to 0-based index
        precedence_graph[u].push_back(v);
    }

    // Get the topological order based on precedence constraints
    if (!topologicalSort(precedence_graph, order)) {
        cout << "No valid topological ordering exists due to a cycle in precedence constraints." << endl;
        return 0;
    }

    vector<int> visited(n, false);
    vector<int> tour;
    int current_city = 0;
    tour.push_back(current_city);
    visited[current_city] = true;

    for (int i = 1; i < n; ++i) {
        int nearest_city = -1;
        int min_distance = numeric_limits<int>::max();
        
        // We loop over cities in the topological order to ensure precedence constraints
        for (int j : order) {
            if (!visited[j] && dist[current_city][j] < min_distance) {
                nearest_city = j;
                min_distance = dist[current_city][j];
            }
        }

        current_city = nearest_city;
        tour.push_back(current_city);
        visited[current_city] = true;
    }

    // Convert 0-based index to 1-based index for output
    for (int &city : tour) {
        city += 1;
    }

    cout << n << endl;
    for (int city : tour) {
        cout << city << " ";
    }
    cout << endl;

    return 0;
}
