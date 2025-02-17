import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import com.baml.cash.cash_core.repository.*;
import com.baml.cash.cash_core.service.LoadReferenceService;
import com.baml.cash.cash_core.model.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Arrays;
import java.util.List;

class LoadReferenceServiceTest {

    @InjectMocks
    private LoadReferenceService loadReferenceService;

    @Mock
    private AcctPlanRefRepository acctPlanRefRepo;
    
    @Mock
    private AcctTypRefRepository acctTypRefRepo;
    
    @Mock
    private ChnCdRefRepository chnCdRefRepo;
    
    @Mock
    private HolidayRefRepository holidayRefRepo;
    
    @Mock
    private ImpacsRsnCdRefRepository impacsRsnCdRefRepo;
    
    @Mock
    private LdgrCdRefRepository ldgrCdRefRepo;
    
    @Mock
    private ProdTypCdRefRepository prodTypCdRefRepo;
    
    @Mock
    private SorCdRefRepository sorCdRefRepo;
    
    @Mock
    private StateIsoRefRepository stateIsoRefRepo;
    
    @Mock
    private TranCdRefRepository tranCdRefRepo;
    
    @Mock
    private TranStatExceptRefRepository tranStatExceptRefRepo;
    
    @Mock
    private TranStatRef1Repository tranStatRef1Repo;
    
    @Mock
    private TranStatRef2Repository tranStatRef2Repo;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetAcctPlanRefData() {
        List<AcctPlanRef> mockData = Arrays.asList(new AcctPlanRef(), new AcctPlanRef());
        when(acctPlanRefRepo.findAll()).thenReturn(mockData);

        List<AcctPlanRef> result = loadReferenceService.getAcctPlanRefData();

        assertNotNull(result);
        assertEquals(2, result.size());
    }

    @Test
    void testGetAcctTypRefData() {
        List<AcctTypRef> mockData = Arrays.asList(new AcctTypRef(), new AcctTypRef());
        when(acctTypRefRepo.findAll()).thenReturn(mockData);

        List<AcctTypRef> result = loadReferenceService.getAcctTypRefData();

        assertNotNull(result);
        assertEquals(2, result.size());
    }

    @Test
    void testGetChnCdRefData() {
        List<ChnCdRef> mockData = Arrays.asList(new ChnCdRef());
        when(chnCdRefRepo.findAll()).thenReturn(mockData);

        List<ChnCdRef> result = loadReferenceService.getChnCdRefData();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetHolidayRefData() {
        List<HolidayRef> mockData = Arrays.asList(new HolidayRef(), new HolidayRef(), new HolidayRef());
        when(holidayRefRepo.findAll()).thenReturn(mockData);

        List<HolidayRef> result = loadReferenceService.getHolidayRefData();

        assertNotNull(result);
        assertEquals(3, result.size());
    }

    @Test
    void testGetImpacsRsnCdRefData() {
        List<ImpacsRsnCdRef> mockData = Arrays.asList(new ImpacsRsnCdRef());
        when(impacsRsnCdRefRepo.findAll()).thenReturn(mockData);

        List<ImpacsRsnCdRef> result = loadReferenceService.getImpacsRsnCdRefData();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetLdgrCdRefData() {
        List<LdgrCdRef> mockData = Arrays.asList(new LdgrCdRef(), new LdgrCdRef());
        when(ldgrCdRefRepo.findAll()).thenReturn(mockData);

        List<LdgrCdRef> result = loadReferenceService.getLdgrCdRefData();

        assertNotNull(result);
        assertEquals(2, result.size());
    }

    @Test
    void testGetProdTypCdRefData() {
        List<ProdTypCdRef> mockData = Arrays.asList(new ProdTypCdRef());
        when(prodTypCdRefRepo.findAll()).thenReturn(mockData);

        List<ProdTypCdRef> result = loadReferenceService.getProdTypCdRefData();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetSorCdRefData() {
        List<SorCdRef> mockData = Arrays.asList(new SorCdRef(), new SorCdRef());
        when(sorCdRefRepo.findAll()).thenReturn(mockData);

        List<SorCdRef> result = loadReferenceService.getSorCdRefData();

        assertNotNull(result);
        assertEquals(2, result.size());
    }

    @Test
    void testGetStateIsoRefData() {
        List<StateIsoRef> mockData = Arrays.asList(new StateIsoRef());
        when(stateIsoRefRepo.findAll()).thenReturn(mockData);

        List<StateIsoRef> result = loadReferenceService.getStateIsoRefData();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetTranCdRefData() {
        List<TranCdRef> mockData = Arrays.asList(new TranCdRef(), new TranCdRef(), new TranCdRef());
        when(tranCdRefRepo.findAll()).thenReturn(mockData);

        List<TranCdRef> result = loadReferenceService.getTranCdRefData();

        assertNotNull(result);
        assertEquals(3, result.size());
    }

    @Test
    void testGetTranStatExceptRefData() {
        List<TranStatExceptRef> mockData = Arrays.asList(new TranStatExceptRef());
        when(tranStatExceptRefRepo.findAll()).thenReturn(mockData);

        List<TranStatExceptRef> result = loadReferenceService.getTranStatExceptRefData();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetTranStatRef1Data() {
        List<TranStatRef1> mockData = Arrays.asList(new TranStatRef1());
        when(tranStatRef1Repo.findAll()).thenReturn(mockData);

        List<TranStatRef1> result = loadReferenceService.getTranStatRef1Data();

        assertNotNull(result);
        assertEquals(1, result.size());
    }

    @Test
    void testGetTranStatRef2Data() {
        List<TranStatRef2> mockData = Arrays.asList(new TranStatRef2(), new TranStatRef2());
        when(tranStatRef2Repo.findAll()).thenReturn(mockData);

        List<TranStatRef2> result = loadReferenceService.getTranStatRef2Data();

        assertNotNull(result);
        assertEquals(2, result.size());
    }
}
