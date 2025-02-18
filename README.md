import io.restassured.RestAssured;
import io.restassured.http.ContentType;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.server.LocalServerPort;
import org.springframework.test.context.ActiveProfiles;

import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test") // Use test profile to avoid modifying real data
public class DisclosuresControllerIT {

    @LocalServerPort
    private int port;

    @BeforeAll
    public static void setup() {
        RestAssured.baseURI = "http://localhost";
    }

    @Test
    public void testGetDisclosures_Success() {
        given()
            .port(port)
            .header("Authorization", "Bearer your_token_here")
            .contentType(ContentType.JSON)
        .when()
            .get("/disclosures")
        .then()
            .statusCode(200)
            .body("$", not(empty())); // Ensure response is not empty
    }

    @Test
    public void testAddDisclosure_Success() {
        String requestBody = "{ \"disclosureData\": \"Sample Disclosure\" }";

        given()
            .port(port)
            .header("Authorization", "Bearer your_token_here")
            .contentType(ContentType.JSON)
            .body(requestBody)
        .when()
            .post("/disclosures")
        .then()
            .statusCode(201)
            .body("id", notNullValue())
            .body("disclosureData", equalTo("Sample Disclosure"));
    }

    @Test
    public void testCreateNewDisclosureVersion_Success() {
        String requestBody = "{ \"disclosureId\": 1, \"newData\": \"Updated Version\" }";

        given()
            .port(port)
            .header("Authorization", "Bearer your_token_here")
            .contentType(ContentType.JSON)
            .body(requestBody)
        .when()
            .post("/disclosures/new-version")
        .then()
            .statusCode(201)
            .body("version", greaterThan(1));
    }

    @Test
    public void testDeleteDisclosure_Success() {
        given()
            .port(port)
            .header("Authorization", "Bearer your_token_here")
            .contentType(ContentType.JSON)
        .when()
            .delete("/disclosures/1")
        .then()
            .statusCode(200);
    }
}
