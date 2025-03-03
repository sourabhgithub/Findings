import org.junit.Test;
import static org.junit.Assert.*;

public class TransactionalUrlTest {

    @Test
    public void testGetTransactionalUrl_TIT1() {
        assertEquals("ECSHWQ1", TransactionalUrl.getTransactionalUrl("TIT1"));
    }

    @Test
    public void testGetTransactionalUrl_TIT2() {
        assertEquals("ECSHWQ2", TransactionalUrl.getTransactionalUrl("TIT2"));
    }

    @Test
    public void testGetTransactionalUrl_TIT3() {
        assertEquals("ECSHWQ3", TransactionalUrl.getTransactionalUrl("TIT3"));
    }

    @Test
    public void testGetTransactionalUrl_PROD_VA() {
        assertEquals("ECSHWQ1", TransactionalUrl.getTransactionalUrl("PROD_VA"));
    }

    @Test
    public void testGetTransactionalUrl_PROD_IX() {
        assertEquals("ECSHWQ2", TransactionalUrl.getTransactionalUrl("PROD_IX"));
    }

    @Test
    public void testGetTransactionalUrl_PROD_AZ() {
        assertEquals("ECSHWQ3", TransactionalUrl.getTransactionalUrl("PROD_AZ"));
    }

    @Test(expected = IllegalArgumentException.class)
    public void testGetTransactionalUrl_UnknownEnv() {
        TransactionalUrl.getTransactionalUrl("UNKNOWN_ENV");
    }
}
