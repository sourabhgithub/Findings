import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;

import javax.sql.DataSource;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class VaultDatabaseProviderTest {

    private static final String ENVIRONMENT_NAME = "DEV";
    private static final String ROLE_ID = "test-role-id";
    private static final String VAULT_URI = "test-vault-uri";
    private static final String VAULT_ONE_DRIVER_CLASS = "test-driver-class";

    @BeforeEach
    public void setup() {
        // Mock static methods
        try (MockedStatic<Constants> mockedConstants = Mockito.mockStatic(Constants.class);
             MockedStatic<VaultSecretRoleIdProvider> mockedRoleIdProvider = Mockito.mockStatic(VaultSecretRoleIdProvider.class);
             MockedStatic<DataSourceUrlPopulation> mockedUrlPopulation = Mockito.mockStatic(DataSourceUrlPopulation.class)) {

            // Mock environment name
            mockedConstants.when(Constants::ENVIRONMENT_NAME).thenReturn(ENVIRONMENT_NAME);

            // Mock roleId provider
            mockedRoleIdProvider.when(() -> VaultSecretRoleIdProvider.getRoleId(anyBoolean())).thenReturn(ROLE_ID);

            // Mock URL population
            mockedUrlPopulation.when(() -> DataSourceUrlPopulation.getBatchUrl(ENVIRONMENT_NAME)).thenReturn("jdbc:test-batch-url");
            mockedUrlPopulation.when(() -> DataSourceUrlPopulation.getReadOnlyUrl(ENVIRONMENT_NAME)).thenReturn("jdbc:test-ro-url");
        }
    }

    @Test
    public void testGetDefaultDataSource() {
        // Act
        DataSource dataSource = VaultDatabaseProvider.getDefaultDataSource();

        // Assert
        assertNotNull(dataSource);
        assertTrue(dataSource instanceof HikariDataSource);

        // Verify that the DataSource is cached
        DataSource cachedDataSource = VaultDatabaseProvider.getDefaultDataSource();
        assertEquals(dataSource, cachedDataSource);
    }

    @Test
    public void testCreateDataSource_Batch() {
        // Act
        DataSource dataSource = VaultDatabaseProvider.createDataSource(true);

        // Assert
        assertNotNull(dataSource);
        assertTrue(dataSource instanceof HikariDataSource);

        HikariConfig config = ((HikariDataSource) dataSource).getHikariConfig();
        assertEquals("jdbc:test-batch-url", config.getJdbcUrl());
        assertEquals(ROLE_ID, config.getUsername());
        assertEquals(VAULT_URI, config.getPassword());
        assertEquals("BatchDataSourceCP", config.getPoolName());
    }

    @Test
    public void testCreateDataSource_NonBatch() {
        // Act
        DataSource dataSource = VaultDatabaseProvider.createDataSource(false);

        // Assert
        assertNotNull(dataSource);
        assertTrue(dataSource instanceof HikariDataSource);

        HikariConfig config = ((HikariDataSource) dataSource).getHikariConfig();
        assertEquals("jdbc:test-batch-url", config.getJdbcUrl());
        assertEquals(ROLE_ID, config.getUsername());
        assertEquals(VAULT_URI, config.getPassword());
        assertEquals("TransactionDataSourceCP", config.getPoolName());
    }

    @Test
    public void testCreateReadOnlyDataSource() {
        // Act
        DataSource dataSource = VaultDatabaseProvider.createReadOnlyDataSource();

        // Assert
        assertNotNull(dataSource);
        assertTrue(dataSource instanceof HikariDataSource);

        HikariConfig config = ((HikariDataSource) dataSource).getHikariConfig();
        assertEquals("jdbc:test-ro-url", config.getJdbcUrl());
        assertEquals(ROLE_ID, config.getUsername());
        assertEquals(VAULT_URI, config.getPassword());
        assertEquals("RO-BatchDataSourceCP", config.getPoolName());
    }

    @Test
    public void testCreateImpalaDataSource() {
        // Act
        DataSource dataSource = VaultDatabaseProvider.createImpalaDataSource(true);

        // Assert
        assertNotNull(dataSource);
        assertTrue(dataSource instanceof HikariDataSource);

        HikariConfig config = ((HikariDataSource) dataSource).getHikariConfig();
        assertTrue(config.getJdbcUrl().contains("UseNativeQuery=1"));
        assertEquals(ROLE_ID, config.getUsername());
        assertEquals(VAULT_URI, config.getPassword());
        assertEquals("ImpalaDataSourceCP", config.getPoolName());
    }
}
