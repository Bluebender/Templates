# Templates
## config

### Configuration de la base de donnée H2

Fichier conf à la racine du projet
```java
@Configuration
public class Conf {

    @Bean
    DataSource getDataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .addScript("create.sql")
                .build();
    }

}
```

### Création de la base de donnée H2

Fichier create.sql dans ressources
```java
CREATE TABLE CONTACT (
    id_contact INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(50) NOT NULL,
    prenom VARCHAR(50)
);
```

## dal

### Methode de création d'un objet dans une base de donnée

```java
@Autowired
private NamedParameterJdbcTemplate jdbcTemplate;

@Override
public void insert(Contact contact) {
	KeyHolder keyHolder = new GeneratedKeyHolder();
	
	MapSqlParameterSource namedParameters = new MapSqlParameterSource();
	namedParameters.addValue("nom", contact.getNom());
	namedParameters.addValue("prenom", contact.getPrenom());

	String sql = "INSERT INTO CONTACT (nom, prenom) VALUES (:nom,:prenom)";

	jdbcTemplate.update(sql, namedParameters, keyHolder);

	if (keyHolder != null && keyHolder.getKey() != null) {
		contact.setIdContact(keyHolder.getKey().intValue());
	}
}
```
## Methode de lecture de plusieurs objets dans une base de donnée

```java
@Autowired
private NamedParameterJdbcTemplate jdbcTemplate;

RowMapper<Contact> rowMapper = (rs,i)-> new Contact(
	rs.getInt("id_contact"),
	rs.getString("nom"),
	rs.getString("prenom")
);

@Override
public List<Contact> getAll() {
	String sql = "SELECT id_contact, nom, prenom FROM CONTACT";
	return jdbcTemplate.query(sql, rowMapper);
}
```

## Classe RowMapper 

```java
@Repository
class AvisRowMapper implements RowMapper<Avis> {

    MembreDAO membreDAO;

    @Autowired
    public AvisRowMapper(MembreDAO membreDAO) {
        this.membreDAO = membreDAO;
    }

    @Override
    public Avis mapRow(ResultSet rs, int rowNum) throws SQLException {
        Avis avis = new Avis();
        avis.setId(Long.parseLong(rs.getString("id")));
        avis.setNote(Integer.parseInt(rs.getString("note")));
        avis.setCommentaire(rs.getString("commentaire"));

        Membre membre = membreDAO.read(Long.valueOf(rs.getString("id_membre")));
        avis.setMembre(membre);

        return avis;
    }
}
```
