import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class ConstantsTest {

    @BeforeEach
    void setUp() {
        // Clear system properties before each test
        System.clearProperty("ENV_DD");
        System.clearProperty("jboss.server.name");
        System.clearProperty("VAULT_CONFIG_FILES");
        System.clearProperty("OUT_PATH");
        System.clearProperty("BATCH_PATH");
        System.clearProperty("UPWALL_PATH");
    }

    @Test
    void testConstantsValues() {
        // Test constant values
        assertEquals("70857", Constants.CASN_ALT);
        assertEquals("ALT_70857_CASH", Constants.CASN_CLIENT_ID);
        assertEquals("A", Constants.ADVICE_INO);
        assertEquals("R", Constants.REVERSAL_INO);
        assertEquals("CASN_ERROR: ", Constants.CASN_ERROR);
        assertEquals("CASN_FATAL: ", Constants.CASN_FATAL);
        assertEquals("CASN_APP", Constants.DATABASE_USER);
        assertEquals("create/static-groups/CASN_APP", Constants.VAULT_UNT);
        assertEquals("secrets/ldap/core/static-crew/70857-", Constants.LDAP_VAULT_UNT);
        assertEquals("READY()", Constants.IMPALL_SERVICE_ID_LIE);
        assertEquals("READY()", Constants.IMPALL_SERVICE_ID_PRO);
        assertEquals("SEMACE1", Constants.KAFKA_SERVICE_ID_CASH_LIE);
        assertEquals("SEMACE2", Constants.KAFKA_SERVICE_ID_CASH_PWD);
        assertEquals("REMANCE1", Constants.KAFKA_SERVICE_ID_EGU_LIE);
        assertEquals("REMANCE3", Constants.KAFKA_SERVICE_ID_EGU_PWD);
        assertEquals("com.bgfg.vault.jdbc.Ondbfive?", Constants.VAULT_ONE_DET_PR_CLASS);
    }

    @Test
    void testEnvironmentName() {
        // Mock system property for ENV_DD
        System.setProperty("ENV_DD", "testEnv");
        assertEquals("testEnv", Constants.ENVIRONMENT_NAME);
    }

    @Test
    void testAppName() {
        // Mock system property for jboss.server.name
        System.setProperty("jboss.server.name", "testApp");
        assertEquals("testApp", Constants.APP_NAME);
    }

    @Test
    void testStaticBlockForVault() {
        // Verify that the static block sets VAULT_CONFIG_FILES correctly
        System.setProperty("ENV_DD", "testEnv");
        assertNull(System.getProperty("VAULT_CONFIG_FILES"));
        assertNull(System.getProperty("OUT_PATH"));
        assertNull(System.getProperty("BATCH_PATH"));
        assertNull(System.getProperty("UPWALL_PATH"));

        // Trigger static block
        new Constants();

        assertEquals("classpath:vault/testEnv.json", System.getProperty("VAULT_CONFIG_FILES"));
        assertEquals("/hosting/apps/vault/data_mn/server-id.txt", System.getProperty("OUT_PATH"));
        assertEquals("/hosting/apps/vault/batch_db/server-id.txt", System.getProperty("BATCH_PATH"));
        assertEquals("/hosting/apps/vault/mn/db/server-id.txt", System.getProperty("UPWALL_PATH"));
    }
}
