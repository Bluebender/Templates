# Templates

## Methode de création d'un objet dans une base de donnée

```java
@Override
public void insert(Contact contact) {
	KeyHolder keyHolder = new GeneratedKeyHolder();
	
	MapSqlParameterSource namedParameters = new MapSqlParameterSource();
	namedParameters.addValue("nom", contact.getNom());
	namedParameters.addValue("prenom", contact.getPrenom());
		jdbcTemplate.update("INSERT INTO CONTACT (nom, prenom) VALUES (:nom,:prenom)", namedParameters, keyHolder);
		if (keyHolder != null && keyHolder.getKey() != null) {
		contact.setIdContact(keyHolder.getKey().intValue());
	}
}

## Methode de lecture de plusieurs objets dans une base de donnée

```java
@Override
public List<Contact> getAll() {
	return jdbcTemplate.query("SELECT id_contact, nom, prenom FROM CONTACT", rowMapper);
}
RowMapper<Contact> rowMapper = (rs,i)-> new Contact(rs.getInt("id_contact"),rs.getString("nom"),rs.getString("prenom"));
