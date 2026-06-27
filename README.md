# Faribank v2 🏦✨

> **Advanced Programming Project 3 – KNTU**  
> Refactored & Extended Neobank Simulation with Investment Funds, Multi-Bank Transfers, Role-Based Access Control, and Pagination.

---

## 📋 Overview

**Faribank v2** is the evolved version of the neobank system developed for the *Advanced Programming* course at K.N. Toosi University. This iteration focuses on **software refactoring**, **architectural improvements**, and **feature expansion** while maintaining backward compatibility with Project2 codebase.

**Core Philosophy**: Incremental improvement via clean OOP design, SOLID principles, and maintainable code—*no monolithic classes, no unjustified static methods*.

---

## 🚀 New Features (v2)

### 📄 Pagination System
- Large result sets (transactions, contacts, users) are paginated (default: 10 items/page).
- Navigation: `< Prev` | `Page N` | `Next >` | `< Back to Menu`.
- Prevents UI clutter and improves performance.

### 💰 Investment Funds (صندوق‌های سرمایه)
| Fund Type | Description | Key Rules |
|-----------|-------------|-----------|
| **Simple Savings** | Basic virtual account for fund storage. | Free deposit/withdrawal from main account. |
| **Remainder Fund** | Auto-saves transaction remainders. | Remainder = `10^k - (last k digits × 0.75)` where `k = floor(log10(amount))`. Only if balance suffices. |
| **Reward Fund** | Fixed-interest savings with lock-in period. | No withdrawal before maturity; monthly profit credited to main account by admin. |

### 📱 Mobile Credit Purchase
- Buy credit for any phone number (self, contacts, or new numbers).
- Deducts amount + tax from main account.
- Credit is tied to phone number, *independent* of Faribank account existence.

### 🔁 Enhanced Money Transfer Methods
| Method | Target | Max Amount | Settlement | Fee |
|--------|--------|------------|------------|-----|
| **Card-to-Card** | Other banks' cards | 100,000 IRR | Instant | 300 IRR |
| **Interbank Pol** | Other banks' cards/accounts | 5,000,000 IRR | Instant | 2% |
| **Interbank Paya** | Other banks' cards/accounts | 5,000,000 IRR | Admin-triggered | 2,000 IRR |
| **Fari-to-Fari** | Faribank accounts/cards | 8,000,000 IRR | Instant | Free |

> ✅ User selects method *after* entering amount & destination, *before* confirmation.

### 👥 Role Enhancements

#### Customer (UNCHANGED + NEW)
- All Project2 features retained.
- + Fund management, mobile credit, multi-method transfers, pagination.

#### Support Agent (REFINED)
- **Section-based permissions**: Each agent assigned to specific modules (`Auth`, `Tickets`, `Users`, `Transfers`, etc.).
- **Unified Requests Module**: Identity verification requests merged into ticketing system; agents only see requests for their assigned sections.
- Cannot create/edit other agents or admins.

#### System Admin (NEW ROLE)
- **Pre-seeded**: One default admin (`admin/admin`); creates all other privileged users.
- **System Settings**: Adjust global parameters (fees, interest rates, pagination size).
- **User Management**: CRUD for all roles; assign support sections; block/unblock users.
- **Auto-Transaction Trigger**: 
  - `Process Paya`: Execute all pending Paya transfers.
  - `Distribute Rewards`: Credit matured Reward Fund profits to main accounts.
- **Admin Hierarchy Protection**: Cannot block parent/superior admins (cycle prevention).

---

## ⚙️ Technical Specifications

- **Language**: Java 11+
- **Architecture**: Refactored OOP with clear separation of concerns (`model`, `service`, `menu`, `util`, `exception`).
- **Time Simulation**: `ir.ac.kntu.util.Calendar` *must* be used for all time operations (accelerated testing).
- **CLI**: Interactive, menu-driven, with pagination and backward navigation. Exit via `quit`.
- **Error Handling**: Contextual messages; graceful recovery; *no crashes on invalid input*.
- **Persistence**: File-based I/O (Project2 compatible); extensible for bonus JDBC/JSON.

### Critical Design Constraints
```java
// ❌ Avoid unjustified static/singleton
public class UserService { // ✅ Instance-based
    private final UserRepository repo;
    public UserService(UserRepository repo) { this.repo = repo; }
}

// ✅ Strong password validation (reusable)
public class PasswordPolicy {
    private static final String PATTERN = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@#$!%*?&]).{8,}$";
    public static boolean isStrong(String pwd) { return pwd.matches(PATTERN); }
}
```

---

## 🔄 Refactoring Guidelines (Mandatory)

1. **Do NOT delete Project2 code**—extend and improve incrementally.
2. **Apply clean-code practices**: meaningful names, single-responsibility classes, documented public APIs.
3. **Avoid static methods** unless mathematically/stateless utilities (e.g., `Math`, `StringUtils`).
4. **Justify Singleton** only for true single-instance resources (e.g., `Calendar`, `ConfigLoader`).
5. **Model first**: Sketch class diagrams (UML/Mermaid) before coding; validate with TA.

---

## 🚀 Installation & Run

```bash
# 1. Clone (forked from Project2)
git clone https://github.com/mahajialirezaei/Project3_AP_KNTU.git
cd Project3_AP_KNTU

# 2. Compile (JDK 11+)
javac -d out src/**/*.java

# 3. Run
java -cp out ir.ac.kntu.faribank.Main
```

> 💡 Use IntelliJ/Eclipse. Import Project2 history; commit refactors as `refactor: extract FundService`.

---

## 🧪 Bonus Features (Extra Credit)

| Feature | Implementation Tips |
|---------|-------------------|
| **Unit Testing** | JUnit 5 for services/validators; mock repositories with Mockito. |
| **Conventional Commits** | `feat(funds): add RewardFund with maturity logic`, `fix(transfer): validate Paya fee`. |
| **Auto-Transactions (Concurrency)** | Use `ScheduledExecutorService` for Paya/reward jobs; ensure thread-safety. |
| **HTML Reports** | Generate transaction tables with CSS; export via `ReportGenerator.exportHTML()`. |
| **Persistent Storage** | JDBC (H2/MySQL) or JSON (Jackson); implement `Repository<T>` interface for swapability. |
| **Admin Hierarchy** | Store `createdBy` reference; block traversal via DFS before blocking. |

> ✅ **Priority**: Core requirements first → bonus features after full test coverage.

---

## 📁 Suggested Structure (Refactored)

```
src/ir/ac/kntu/faribank/
├── Main.java
├── menu/               # CLI navigation (paginated)
├── model/              # User, Account, Fund, Transaction, Ticket, Contact
├── model/fund/         # SimpleFund, RemainderFund, RewardFund (inheritance)
├── model/transfer/     # TransferMethod enum, PayaTransfer, PolTransfer
├── service/            # AuthService, FundService, TransferService, TicketService
├── service/admin/      # AdminUserService, AutoTransactionService
├── util/               # Calendar (provided), Validator, ColorPrinter, Paginator
├── exception/          # InsufficientBalanceException, InvalidFundOperationException
└── config/             # SystemConfig (fees, rates), AdminHierarchyGuard
```

---

## 📚 Resources

- [Java JDBC Tutorial](https://www.baeldung.com/java-jdbc)
- [SQL Basics](https://www.w3schools.com/sql/)
- [JSON in Java](https://www.baeldung.com/java-org-json)
- [Refactoring Guru: Singleton](https://refactoring.guru/design-patterns/singleton)
- [Java Concurrency](https://www.digitalocean.com/community/tutorials/multithreading-in-java)
- [Clean Code Principles](https://gist.github.com/qoomon/5dfcdf8eec66a051ecd85625518cfd13)

---

## 🤝 Contributing

1. Fork the repo
2. Create feature branch: `git checkout -b feat/reward-fund-maturity`
3. Commit with conventional format + clean code
4. Push & open PR with description of refactoring + new features

> 🔒 **Code Review Checklist**:  
> - [ ] No monolithic classes  
> - [ ] Static methods justified  
> - [ ] Pagination implemented for large lists  
> - [ ] All error cases handled with user-friendly messages  
> - [ ] `Calendar` used for time operations  

---

*Faribank v2 – Building scalable, maintainable financial software, one refactoring at a time.* 💳🔧
