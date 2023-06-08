# demo
@RestController
@RequestMapping("/api/user")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/data")
    public ResponseEntity<UserDataResponse> getUserData() {
        UserDataResponse userData = userService.getUserData();
        return ResponseEntity.ok(userData);
    }
}



//////////////////////////////////////
service


@Service
public class UserService {

    private final UserRepository userRepository;
    private final AuthenticationFacade authenticationFacade;

    public UserService(UserRepository userRepository, AuthenticationFacade authenticationFacade) {
        this.userRepository = userRepository;
        this.authenticationFacade = authenticationFacade;
    }

    public UserDataResponse getUserData() {
        String username = authenticationFacade.getUsername();
        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found"));

        // Map user data to UserDataResponse object
        UserDataResponse userDataResponse = new UserDataResponse();
        userDataResponse.setName(user.getName());
        userDataResponse.setFatherName(user.getFatherName());
        userDataResponse.setAccountNumber(user.getAccountNumber());
        userDataResponse.setCardNumber(user.getCardNumber());

        return userDataResponse;
    }
}


<!-- repository -->

@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    Optional<User> findByUsername(String username);
}


<!-- entity -->


@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username")
    private String username;

    @Column(name = "name")
    private String name;

    @Column(name = "father_name")
    private String fatherName;

    @Column(name = "account_number")
    private String accountNumber;

    @Column(name = "card_number")
    private String cardNumber;

    // Constructors, getters, and setters
}

<!-- AuthrnticationFacade -->

